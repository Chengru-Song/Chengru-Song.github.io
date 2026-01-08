---
layout: article
title: 【AI】最近RL的一些发展
sidebar:
  nav: AI
aside:
  toc: true
key: llm
date: 2025-09-04 17:17:07 -0700
tags:
- 301-work-ai
category: [AI, AI_Algorithms, Vision]
typora-root-url: ../../../blog
mermaid: true
---

# 最近VLM Post-train RL的一些发展

## 前言

最近在开源社区涌现出了非常多性能很好的开源VLM，无一例外都在RL阶段引入了Reasoning能力，但各家的方法都不一样，RL本身目前属于百花齐放的状态，还没有走到类似于Pretrain和SFT非常标准化的流程，除了数据需要探索之外，如何最大化发挥Reasoning能力，并同时保证Human Alignment，是各家都在探索的话题。这篇文章不追求能够涵盖所有最先进的RL方法，但是**致力于把最新的进展讲清楚，归纳好**，这样在做技术规划的时候可以比较游刃有余。



## 数据

1. Intern-S1
   1. Pretrain: 如何组织large scale的Science domain的数据
      1. 使用Agent flow（就是做了个数据策略），从开源数据集里进行recall和filter，把target domain的数据从2%提升到50%比例；
      2. 课本的PDF documents，解析出了2.5T tokens
   2. Post-train（作者直接把best-of-N数据筛选SFT直接叫做offline RL……其实还是先SFT再RL的）
      1. mixture验证：
         1. 原子能力验证：在InternLM3的SFT数据基础上加入数学的，看有没有数学领域的提升；
         2. 混合能力验证：curriculum learning + 基于benchmark评分的sampling and training；
   3. Online-RL
      1. 核心思考，不同任务的Reward差异巨大，无法直接放到一起训练，所以怎么能normalize这个Reward比较关键；
      2. 数据收集：使用InternBootCamp的数据mixture，包含1000+任务种类
      3. 如何设计rewards：数据分成三个大类
         1. Instruction following类：直接[IFDECORATOR](http://www.arxiv.org/pdf/2508.04632)数据集，里面包含了IF难度和遵循验证的方式；
         2. Reasoning：使用CompassVerifier，选择、数值、短答、公式、子题、序列、布尔这几类都能验证；
         3. Alignment：使用[POLAR-7B](https://arxiv.org/pdf/2507.05197)模型获得relative ranking
      4. 训练手法
         1. GRPO的token clipping会导致训练崩溃：SFT loss + 负样本policy gradient
         2. 效果：比DAPO收敛更快；
         3. 所有数据都是用了think流程，只是open-ended问题没有用think的内容，直接用了结果
2. InternVL 3.5
   1. Pretrain：text:mm = 1:2.5
   2. SFT
      1. 数据组织，IF数据 + Reasoning数据（过滤了thinking不clear的，冗余的reason process）+ 专有任务数据
   3. RL
      1. 整体思路：先做offline RL，再做Online RL
      2. MPO做offline RL，改了loss，变成了preference loss + generation loss + 分类loss
      3. Online RL：GSPO，提升了训练稳定性；
   4. ViCO：一个为了提升自己flash模型的专有训练，不太需要关注；
3. GLM-4.5V
   1. Pretrain: a) 多模态预训练：seq len 8,192；全局 batch 1,536；共 120k steps；GLM-4.1V-Thinking 用 TP=2；GLM-4.5V（MoE）用 EP=8、PP=4；采用样本拼接的数据打包策略。 b) 长上下文续训：把序列长度扩到 32,768，并加入视频与 8k+ 的图文交错样本；继续训练约 10k steps，batch 仍 1,536；并开启 context parallel size=4。
      1. ![img](https://emb8kawbf2.feishu.cn/space/api/box/stream/download/asynccode/?code=MDI4YWU2YjM3MmZkNzdhZTA2NGI5OGMxMTMwY2E1MmFfNEQzQXBDVWVqSmVuQ0RjQ1U2bWNmeXNTVTUwVXkxN0FfVG9rZW46RXhCQmJjdHR6b3hSenV4TExsaGNLc1dVbjdnXzE3NTcwMzEyNDY6MTc1NzAzNDg0Nl9WNA)
   2. SFT
      1. 在SFT阶段使用了/think和/nothink指令来让模型做出不同的判断；
      2. 使用/nothink，把结果放到<answer>tag里面，但是empty掉think的content，导致结果更好（这到底是因为有thinking mode的数据本身就高质量还是只是因为follow这个format导致的？）
         1. ![img](https://emb8kawbf2.feishu.cn/space/api/box/stream/download/asynccode/?code=YjU1MmNmM2E3M2Y0YTY4MDIwY2VhNjZkMjY5ZmFkYjFfUU5XbVp6dEZDZFo2aFlXbXBSMnZqa0g0NHdSZ3VIVG1fVG9rZW46TmNuSmI1aEFwb2t4cTN4VGNlemNkc1R0bnZmXzE3NTcwMzEyNDY6MTc1NzAzNDg0Nl9WNA)
   3. RL
      1. 整体思路：把所有任务都放到RLVR的框架里训练，构架统一的Reward System
      2. 挑战
         1. RLHF的Reward signal不好设计：直接使用Reward Model，但要考虑好，Reward的精度、一致性
         2. Domain-specific的Reward不好设计：不同Domain的Reward单独进行unittest
         3. 训练时候会出现Reward hacking：RM会倾向于给某些回复大正确的label；
         4. 任何一个任务设计不合理Reward，就会导致训练崩溃：
         5. 训练一阵子之后，ACC就会饱和：设计了curriculum RL，直接把prompt分层，不同阶段prompt训练；
         6. 训练效率比较低（训练很多后ACC不涨）：更大batch-size，dynamic samling(训练过程中，根据模型上一个batch`训练的成功率确定下一个`batch的难度），过长的回复force插入answer token，KL loss不要加，CLIP可以做高一些；
         7. 训练不稳定：SFT数据很关键，否则模型会不断输出无意义thinking；top_p=1 stable training；per-sample和per-token的loss对训练效果的影响不大；
         8. 不要加Format Reward，确保SFT能学对：不然模型会比较懵逼，
4. Mimo-VL
   1. RL
      1. 整体思路：把所有Reward 都nomalize到scalar value，然后统一使用RLVR方法训练
      2. Reward Verify：Math-verify lib
      3. RLHF：构建Reward Model
      4. RLVR：使用math-verify lib构建RLVR结果
5. Ovis 2.5
   1. RL
      1. 整体思路：先进行Offline DPO，在进行RLVR训练
      2. DPO: 多任务数据，Reasoning的tasks，使用vanilla cot和thinking style cot；General purpose的task，LLM scoring
      3. RLVR：Math + Science QA + visual QA问题，组织了一批问题是来自于图片，而不是文本的问题；对于多选题，改造结果为fill-in-blank的Format，防止Reward hacking



## Training Stage