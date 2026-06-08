---
layout: article
title: 【AI】Thinking of GRPO
sidebar:
  nav: AI
aside:
  toc: true
key: llm
date: 2026-05-16 10:51:07 -0700
tags:
- 301-work-ai
category: [AI, AI_Algorithms, Vision]
typora-root-url: ../../../blog
mermaid: true
published: false
---

# VLM的GRPO-like算法，有本质效果提升的不多

## 前言

开源的paper关于算法本身的研究中，VLM(Vision Language Model)相关的RLVR相关验证较少，因此最近在大量的VLM的GRPO优化算法上做了不少研究，也进行了大量相关的论文阅读，下一篇可以细数一下相关论文，发个简单的Literature Review出来，但目前来看，我想先输出下面一些暴论

1. **能改数据，就先不动GRPO算法**，数据本身的组成带来的影响大于GRPO算法本身。
2. **大部分基于GRPO的算法改进，不触及本质**，很多甚至效果更差，甚至在论文中，也只能说提升了训练的token Efficiency，但效果上就是偏波动；
3. **牢记奥卡姆剃刀原则**：Trick越多，泛化性越差，你说效果好，更大可能不是算法提升，而是过拟合在你的数据，你的初始模型等和算法无关的超参上面了。
4. **所有可能的问题，都已经出现在改进公式里了**：仔细研究自己的公式是不是有问题，很可能为了解决一个问题，带来了更严重的问题。

当然，上面是一个低情商的表达，高情商的表达是，只要在合适的数据和初始模型上，所有算法都有自己work的空间。

## 实验设置

本文提到的实验，都是在Qwen3-VL，30B-A3B的MoE模型上进行的实验，专门选取了MoE模型，是想看看MoE的训练挑战是否真的比Dense模型大很多，同时观测下训练过程中的MoE相关各项指标是否会出现极大的劣化。Training Batch Size=256，每条样本Rollout 8条。主要研究算法为GRPO，DAPO和GSPO，其中GSPO声称对MoE模型的效果更好，因为GSPO的Importance sampling是作用在Sequence Level的，如果Routing不稳定，那么整条trajectory的IS会比较高，这条Sequence会在实际更新的时候被clip掉，但其局限也在于GSPO的IS correction本身对routing-changed tokens是结构性无效的，sequence-level clip只是一个保守性的安全阀，而不是对routing distribution shift的真正修正。所以只能说可能有效，但是否真的效果显著，还得实际做的时候通过实验验证。



