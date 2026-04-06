---
layout: article
title: 【AI】DPO实战Sharing
sidebar:
  nav: AI
aside:
  toc: true
key: llm
date: 2026-04-06 13:11:07 -0700
tags:
- 301-work-ai
category: [AI, AI_Algorithms, Vision]
typora-root-url: ../../../blog
mermaid: true

---

# VLM RL算法的思考（三）

之前分享过很多和DPO训练相关的文章，DPO在数据组织方式上存在巨大优势（主要两点，可离线收集，只需要Rollout两条数据即可），在训练效率上相比PPO和GRPO也有相对优势（比PPO少load一个Critic Model和Reward Model，比GRPO Rollout次数少），外加天然比较适配做业务的AB实验组，因此是一个快速开展业务RL的起步算法，或者至少是一个值得对比的算法baseline。

但DPO本身的问题实在是比较多，因此这篇文章在之前文章的基础上，又深化了一下基于理论而不是单纯在实践上的思考，分享出来可供大家实操。

## 本文目标

1. 明确DPO算法本身存在怎样的设计缺陷；
2. 回顾DPO的发展历程，和DPO算法相关发生过怎样的优化；
3. 针对业务场景，到底什么优化是管用的，我们应该如何调参。

## 背景：DPO存在怎样的设计缺陷？

先说结论

> [!WARNING]
>
> 1. 正优化失效：Rejected降的速度快于Chosen升的速度，导致Acc上涨，但实际上Policy Model的回复更差了（Format约束失效）；
> 2. 长度偏置：较长样本天然具有更高rewards，导致DPO优化后模型输出变长，甚至停不下来；
> 3. Reward Hacking。
> 4. 训练效果不稳定，常出现相同参数无法适配到所有任务中。



为什么DPO会存在以上缺陷？

从DPO的loss公式出发，DPO天然被定义为，最大化Policy的Reward，同时用KL散度保证待优化的Policy不要太远离ref Policy。

$$\max_{\pi_\theta} J(\pi_\theta) = \mathbb{E}*{x\sim\mathcal{D}, y\sim\pi*\theta(y|x)} \left[ r(y|x) - \beta \cdot D_{\text{KL}}\left( \pi_\theta(y|x) \parallel \pi_{\text{ref}}(y|x) \right) \right] $$

将KL散度展开可得，对于每个独立的$$x $$(prompt)，优化目标为

$$\max_{\pi_\theta(\cdot|x)} \sum_y \pi_\theta(y|x) r(y|x) - \beta \sum_y \pi_\theta(y|x) \log \frac{\pi_\theta(y|x)}{\pi_{\text{ref}}(y|x)} $$

s.t. $$\sum_y \pi_\theta(y|x) = 1 $$

**注意这里的比较容易迷惑的点**：限制条件是，针对输入$$x $$ ，所有可能的回复$$$$的总概率为1。而基于Policy Gradient或者Natural Gradient的RL算法来说，一般来说都是Action粒度的，每个Action的优势都是需要估计的。所以虽然都是优化一条回复的Reward，但是Policy Gradient一般来说不这么建模。

DPO具体推导过程参考Reference

对我们有用的是最终的Loss function为

$$\mathcal{L}*{\text{DPO}}(\pi*\theta; \pi_{\text{ref}}) = - \mathbb{E}*{(x,y_w,y_l)\sim\mathcal{D}} \left[ \log \sigma\left( \beta \log \frac{\pi*\theta(y_w|x)}{\pi_{\text{ref}}(y_w|x)} - \beta \log \frac{\pi_\theta(y_l|x)}{\pi_{\text{ref}}(y_l|x)} \right) \right]$$

把比值简化能得到下列公式，在实现的时候就是

$$\mathcal{L}*{\text{DPO}} = - \mathbb{E}*{(x,y_w,y_l)} \left[ \log \sigma\left( \hat{r}*\theta(y_w|x) - \hat{r}*\theta(y_l|x) \right) \right]$$

实际实现时，loss = sum(logps(chosen) - sum(lops(reject))

单条样本的梯度公式：

$$\nabla_\theta L = - \beta \cdot \sigma(-\Delta_\theta) \cdot \left( \nabla_\theta \log \pi_\theta(y_w|x) - \nabla_\theta \log \pi_\theta(y_l|x) \right)$$

$$\nabla_\theta \mathcal{L}*{\text{DPO}} = -\beta \cdot \underbrace{\left( 1 - \sigma\left( \beta \cdot \Delta_r \right) \right)}*{\text{样本权重项}} \cdot \underbrace{\left( \nabla_\theta \log \pi_\theta(y_w|x) - \nabla_\theta \log \pi_\theta(y_l|x) \right)}_{\text{偏好梯度方向}}$$

对于以上缺陷，从Loss和Gradient角度出发理解

1. Loss本身只鼓励Chosen和Reject的Margin，并不实际能增大Chosen输出的概率，在BT概率模型中，训练RM没有问题，但是DPO这样使用，就出现了问题；
2. 在样本梯度公式中，$$\beta$$ 扮演了如下的角色，因此会出现天然悖论：**当你想让模型更保守的时候，梯度会变大，训练不稳定；当你想让模型更激进的时候，梯度较小，sigmoid很平，样本都学但KL弱，可能偏离ref特别大，还是会变差。**

| **β 大**                | **β 小**             |
| ----------------------- | -------------------- |
| 梯度大                  | 梯度小               |
| sigmoid 很陡（hard）    | sigmoid 很平（soft） |
| 只学错的样本            | 所有样本都学         |
| KL 强（贴近 reference） | KL弱                 |
| 更保守                  | 更激进               |

| **Δᵣ 的值** | **含义**                        |
| ----------- | ------------------------------- |
| Δᵣ ≫ 0      | 模型已经能很好区分 yᵥ over yₗ   |
| Δᵣ ≈ 0      | 模型不能区分win和lose           |
| Δᵣ ≪ 0      | 模型非常prefer yₗ，gradient巨大 |

**全是太难的样本，将直接导致DPO训练崩溃；全是太简单的样本，DPO训练不会更新模型权重，等于没学；中间的样本，DPO能学的比较好，梯度比较稳定，但如何调整**$$\beta$$ **成为一门手艺。**

## 如何解决以上问题？

| **时间**   | **暴露的问题**                                    | **代表工作/方向**                                            | **核心思路**                                               |
| ---------- | ------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------------------------------- |
| 2024 Q1    | DPO 理论近似有偏，BT 假设太强                     | IPO, KTO                                                     | 改偏好建模/改 loss，不再完全信 DPO 原始推导                |
| 2024 Q1-Q2 | reference 成本高、限制强                          | ORPO, SimPO                                                  | 去 reference 或弱化 reference，降低内存和约束              |
| 2024 Q2    | 排序没学好                                        | Preference Learning Algorithms Do Not Learn Preference Rankings | 指出 DPO 不一定真修正 preference ranking                   |
| 2024 Q2    | rejected 降得快于 chosen 升得快                   | Theoretical Perspective on DPO Limitations                   | 揭示梯度不平衡，为后续 BDPO 铺路                           |
| 2024 Q2-Q3 | 长度偏置                                          | Disentangling Length from Quality, SamPO                     | 去 verbosity bias / length exploitation                    |
| 2024 Q2-Q4 | pairwise 太粗糙，数据利用率低                     | RPO, mDPO                                                    | 用相似 prompt、多样本比较补信息                            |
| 2024 Q3    | reference 到底怎么影响 DPO                        | Understanding Reference Policies in DPO                      | 说明 reference 既稳又会带来 ceiling/mismatch               |
| 2025 Q1    | 训练注意力分配不合理                              | FocalPO                                                      | 动态重加权样本，改善 ranking 学习                          |
| 2025 Q2    | data utilization 不佳                             | Pre-DPO, Omni-DPO                                            | 用 guiding reference / 动态样本权重提升数据效率            |
| 2025 Q2    | rejected influence 过强                           | BDPO                                                         | 给 rejected 项加边界，平衡 chosen/rejected 学习            |
| 2025 Q2-Q3 | 偏好多样、群体冲突                                | GDPO, λ-DPO                                                  | 从单峰偏好转向 group/listwise/multi-dimensional preference |
| 2024–2026  | 偏好数据噪声                                      | Distributionally Robust DPO, wDPO                            | 用 DRO、winsorization 做鲁棒训练                           |
| 2026 Q1    | pessimistic reference 导致 premature satisfaction | HyPO                                                         | 条件性 debias reference，而非完全去掉                      |
| 2026 Q1    | 只有 pairwise 胜负太浪费                          | RDPO/RIPO                                                    | 加入 rating gap，提升统计效率                              |
| 2026 Q1    | OOD / confounder 问题                             | CausalDPO                                                    | 用因果不变性提升分布外泛化                                 |

## Experiments

| beta=0.1           | Math  |
| ------------------ | ----- |
| DPO baseline       | $$\mathcal{L}_{\text{DPO}} = - \mathbb{E}{(x, y^+, y^-)} \left[ \log \sigma \left( \beta \cdot \left( \log\pi_\theta(y^+|x) - \log \pi_\theta(y^-|x) \left(\log \pi_{\text{ref}}(y^+|x) - \log \pi_{\text{ref}}(y^-|x)\right) \right) \right) \right] $$ |
| DPO + SFT Baseline | $$ L=\alpha \mathcal{L}_{\text{DPO-LN}} + \beta \mathcal{L}_{\text{SFT}} $$     |
| DPO + BCO baseline | $$ L=\alpha \mathcal{L}_{\text{DPO-LN}} + \gamma\mathcal{L}_{\text{BCO}}  $$    |
| MPO Baseline       | $$ L=\alpha \mathcal{L}_{\text{DPO}} + \beta \mathcal{L}_{\text{SFT}} + \gamma\mathcal{L}_{\text{BCO}}   $$   |
| SimPO              | $$ \mathcal{L}_{\text{SimPO}} = -\mathbb{E}_{(x,y_w,y_l)\sim \mathcal{D}} \left[ \log \sigma \left(\beta \left(\frac{1}{|y_w|} \log \pi_\theta(y_w \mid x) -\frac{1}{|y_l|} \log \pi_\theta(y_l \mid x) \right)- \gamma \right) \right ] $$      |
| OrPO               | $$ \mathcal{L}_{\mathrm{ORPO}}(\theta)=\mathbb{E}_{(x,y_w,y_l)\sim\mathcal{D}}\left[- \log \pi_\theta(y_w \mid x)+ \lambda \log \sigma \left(\log\frac{\pi_\theta(y_w\mid x)}{\pi_\theta(y_l \mid x)}\right)\right]  $$    |
| DPO Length Norm    | $$ \mathcal{L}_{\text{DPO-LN}} =- \mathbb{E} \left[\log \sigma \left(\beta \cdot \left(\frac{\log \pi_\theta(y^+)}{L_+^{\alpha}}-\frac{\log \pi_\theta(y^-)}{L_-^{\alpha}}-\left(\frac{\log \pi_{\text{ref}}(y^+)}{L_+^{\alpha}}-\frac{\log \pi_{\text{ref}}(y^-)}{L_-^{\alpha}}\right)\right)\right)\right]  $$    |
| MPO Length Norm    |  $$ L=\alpha \mathcal{L}_{\text{DPO-LN}} + \beta \mathcal{L}_{\text{SFT}} + \gamma\mathcal{L}_{\text{BCO}}    $$ |



## Learnings

1. 数据
   1. 正负样本标注需用不同模型组合或者真实信号，避免Reward hacking；
   2. 正负样本之间的margin不要过大，最好能在模型的自Context上学习，避免强模型输出内容distribution偏离过大
2. 训练
   1. $$\beta$$和LR要联合调整，建议beta从0.1开始，分别向0.05和0.2两边调整，如果中间都优化不好，最好调整解决一下数据问题。

## What's Next？

**第一，vanilla DPO  baseline效果从优化角度很好，各种优化的Acc大概率都比Vanilla DPO差，但实际应用没办法直接使用。**因为真实偏好远比 pairwise logistic + static reference 更复杂，并且其隐含在baseline loss function里面的固有问题是严重的。

**第二，后续优化正在把 DPO 从“一个公式”变成“一个框架”。** 你现在看到的主要增强件，已经覆盖了 reference design、sample weighting、noise robustness、pluralistic preference、listwise/rating supervision、OOD generalization。

**第三，最有前景的方向不是单点改 loss，而是“更丰富监督 + 更合理数据选择 + 更精细 reference 使用”。** 也就是从纯 pairwise DPO，走向 listwise/rating/multi-sample；从 uniform weighting，走向 difficulty-aware / quality-aware weighting；从 static reference，走向 conditional / guiding / debiased reference。

## Reference

所以原问题被转化为这是一个带等式约束的泛函优化问题，我们构造拉格朗日函数：

$$\mathcal{L} = \sum_y \pi_\theta(y|x) r(y|x) - \beta \sum_y \pi_\theta(y|x) \log \frac{\pi_\theta(y|x)}{\pi_{\text{ref}}(y|x)} + \lambda \left( 1 - \sum_y \pi_\theta(y|x) \right)$$

其中$$\lambda$$是拉格朗日常数。

对$$\pi_\theta(y|x$$求偏导，极值为0时L取得最大值，可得

$$\frac{\partial \mathcal{L}}{\partial \pi_\theta(y|x)} = r(y|x) - \beta \left( \log \frac{\pi_\theta(y|x)}{\pi_{\text{ref}}(y|x)} + 1 \right) - \lambda = 0$$

变形后两边取指数：

$$\pi_\theta(y|x) = \pi_{\text{ref}}(y|x) \cdot \exp\left( \frac{r(y|x)}{\beta} \right) \cdot \exp\left( -\frac{\lambda + \beta}{\beta} \right)$$

根据归一化条件$$\sum_y \pi_\theta(y|x) = $$，定义归一化配分(partion function)函数：

$$Z(x) = \sum_y \pi_{\text{ref}}(y|x) \exp\left( \frac{r(y|x)}{\beta} \right)$$

最终得到DPO最优Policy的closed Form：

$$\pi^*(y|x) = \frac{1}{Z(x)} \cdot \pi_{\text{ref}}(y|x) \cdot \exp\left( \frac{r(y|x)}{\beta} \right)$$

两边同时取对数可以得到

$$\log \pi^*(y|x) = \log \pi_{\text{ref}}(y|x) + \frac{r(y|x)}{\beta} - \log Z(x)$$