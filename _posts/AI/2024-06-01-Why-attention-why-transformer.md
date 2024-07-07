---
layout: article
title: 【AI】Why attention, why transformer
sidebar:
  nav: AI
aside:
  toc: true
key: llm
date: 2024-06-01 14:34:07 +0800
tags:
- 301-work-ai
category: [AI, AI_Algorithms, VAE]
typora-root-url: ../../../blog
mermaid: true
---

# Why Attention, why Transformer

> - 时间：2024.05.13
> - 作者团队：MIT
> - 作者：Minyoung Huh,  Brian Cheung,  TongzhouWang, Phillip Isola
> - 有用指数：⭐️⭐️⭐️⭐️⭐️
> - 贡献程度：⭐️⭐️⭐️⭐️⭐️
> - 简单评价：用一个简洁的假设解释了很多问题，也预测了一些趋势，其中不乏较为详实的说明和严谨的推论。对于还在疑惑为什么大模型可以work的朋友，是一个很好的知识补充。

上一篇，The Platonic Hypothesis，主要讲了好的大模型表征都在收敛这个结论。虽然整体都很精彩，但整体上还是会提出几个疑问

1. 收敛的原因是否也和架构逐渐趋同有关？
   1. 现在的模型架构也是极其相似的，Transformer有大一统的趋势；
   2. 后面的几个引论假设都是建立在如果只有能胜任多任务的大模型在收敛，为什么那个Model stitching的实验能够在简单的数据集上训练的模型上work？
2. 为什么是Transformer？
   1. Scale模型规模做的最好的无疑是Transformer的架构，那么这个架构到底有什么优秀的地方？



## Why Transformer Scale

### 影响深度网络Scale的原因是什么

