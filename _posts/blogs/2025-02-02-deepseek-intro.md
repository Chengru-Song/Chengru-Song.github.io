---
layout: article
title: 【Blog】Deepseek完整版介绍
aside:
  toc: true
key: work_reflection
date: 2025-02-02 17:36:07 +0800
tags:
- 301-work-blog
category: [work, Blog]
typora-root-url: ../../../blog
---

# Deepseek完整版介绍

## TLDR

1. 打破LLM训练从Generalist到Reasoner的常规思路，使用RL先得到Reasoner，再经过SFT成为Generalist。





## R1-Zero是如何成为Reasoner的？

一句话：大基座（671B MoE模型） + Rule-based Reward + GRPO RL算法。

1. 大基座，基座是Deepseek-V3-Base模型，



## R1-Zero都做了哪些事情？



## R1做了哪些事情



## R1能Work的原因可能是什么？



## R1技术迭代路径是什么？



## 没透露的技术细节是什么？



## 有什么试图复现R1的工作？效果如何？

### TLDR

OpenR1是目前唯一一个试图严谨复现R1的工作，其他基于某个特定场景（例如24点）Toy project复现的，复杂度过低，即使出现了部分reasoning能力，但相比R1-Zero都存在巨大的Gap。R1-Zero在我看来的精髓是，各种不同的任务之间互有Boost，从而使RL出现了泛化。

这里并非贬损已有的复现工作，其中一些工作的结果非常有参考意义，后面会详细介绍。但是距离对R1的完整认知，还有非常远的路要走。

### 复现的定义是什么？

在某个大小的模型上，观测到**Self-verification和Search solution space的涌现能力**。同时在任务上的Score随着训练准确率提升。其中一个观测指标是，回复长度随时间增加的Scaling曲线，如Deepseek放出来的。![image-20250201143651410](/assets/images/image-20250201143651410.png)

1. TinyZero：https://github.com/Jiayi-Pan/TinyZero by Jiayi Pan(Standford PhD)
   1. Key insights：
      1. 在Instruct模型上跑出来了thinking和steps的scaling，而且Instruct模型的训练过程明显更稳定。**说明SFT冷启动非常重要！**![image-20250205164818004](/assets/images/image-20250205164818004.png)
      2. 一点遗憾：Thinking的scaling没带来结果上的变好，validation更差了：![image-20250205165030283](/assets/images/image-20250205165030283.png)

   2. 模型大小：**Qwen2.5-0.5B模型和Qwen2.5-3B**
   3. 场景：24点游戏，给定四个数字和结果，通过四则运算得到该结果。
   4. 结论：
      1. 有CoT过程，有Self Reflection和Solution space Search，但是没有看出来涌现能力。![image](/assets/images/cover.png)
      2. 有借鉴意义，但24点游戏对于RL是非常简单的任务，不通过R1-Zero的reasoning也能做出来，但另一个侧面想，小模型在小任务上能跑出来已经是了不起的成就了，Scale模型也许就能在更多任务上跑出更好的结果。

2. GRPO demo by Will Brown
   1. Key insights: 使用好的System prompt + 较低KL coef
   2. 结论：使用GRPO，在GSM8K上相比原生从41.6%提升到了51%。但是没有提直接SFT带来的提升。
   3. Reward：使用了int Reward + Format Reward  + correctness Reward。

3. SFT Memorizes, RL Generalizes (Tianzhe Chu HKU, ): https://tianzhechu.com/SFTvsRL/
   1. Key insights: RL在规则泛化性上更好（但没有说明是CoT带来的，文中也没有任何跟CoT有关的讨论，应该是R1之前就在写的文章，只是R1出来后发出来了）。
      1. 训练scaling曲线![image-20250205233522134](/assets/images/image-20250205233522134.png)

   2. 模型大小：LLama-3.2-Vision-11B。
   3. 作者Design了两个场景，一个是纯文本的`General Points`，通过在Inference时候临时改变规则，观测模型的泛化性（比如JQK是代表11,12,13还是全都是10）。另一个是Vision场景的辨别，在地图上把原先东北转向变成Slight Right，看模型还能否做对。
   4. 讨论
      1. 24点改规则的游戏，用了RL还是低的离谱。Acc从11.5%到15%，SFT从11.5%掉到了3.4%。要知道不用LLM，纯RL做24点都比这个效果好。
      2. Vision任务，不如改规则的说服力强，原因是语言模型原本可能包含对northeast和Slight Right的语义理解，经过SFT之后强化了northeast，丢失了语义能力。而且SFT之后，OOD还不如Baseline（80.8%->1.3%），大概率说明的是SFT有问题，还谈不上SFT比不上RL。
      3. 从理论角度讲，NTP loss能训练成一个能做题的或者做好一个环境互动任务的本来就不make sense，但只要建模没问题，RL能做好就是较为正常的事情。所以文章的说服力并不是很强。
4. OpenR1：https://huggingface.co/blog/open-r1
   1. 目标：补齐R1 Pipeline里没讲清楚的事情，完全复现R1的结果
   2. 预期动作
      1. 复现R1蒸馏模型：从R1蒸馏等量SFT数据，在Qwen模型上Finetune，取得和R1 Tech report里面用SFT蒸馏的Qwen相同指标；
      2. 复现R1-zero：用Math，reasoning和code数据训练得到R1-Zero。
      3. 复现R1：根据R1-Zero的复现结果生成Reasoning data，再复现完整的R1 Pipeline。![img](/assets/images/plan-of-attack.png)

   3. 评价
      1. OpenR1是一个比较严肃的，试图复现R1完整工作的流程，是一个复杂度非常高，难度很大的项目。个人认为能够做到Open R1 Zero就已经成功了，R1能达到现有水平的80%水位就是完美。一个很重要的原因是SFT阶段的数据不太好做，Deepseek内部应该做了非常多工作，想要低成本打平数据上的Gap是很难的。光R1-Zero里面的数据，需要每一条都验证正确性，Coding的还要过编译器，组织这些数据已经是非常非常难的工作了。



## 下一步能尝试的事情是什么？



## 部分背景知识



## Reference

