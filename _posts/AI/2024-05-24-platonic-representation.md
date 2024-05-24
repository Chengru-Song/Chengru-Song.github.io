---
layout: article
title: 【AI】The Platonic Representation Hypothesis
sidebar:
  nav: AI
aside:
  toc: true
key: llm
date: 2024-05-24 14:34:07 +0800
tags:
- 301-work-ai
category: [AI, AI_Algorithms, VAE]
typora-root-url: ../../../blog
mermaid: true
---

# 为什么多模态Alignment Module有用

> - 时间：
> - 作者团队：
> - 作者：
> - 有用指数：⭐️⭐️⭐️⭐️⭐️
> - 贡献程度：⭐️⭐️⭐️⭐️⭐️
> - 简单评价：

## 出发点

科学的发展总是基于一些可证伪的假设的证明或推翻，例如经典力学解释不了的现象，在微观粒度会有量子力学的理论进行支撑。某些看似不可能改变的理论，也仅在某些时空维度下是成立的。这篇文章就提出了一个假设，即，**好的大模型的表征是正在收敛的，包括不同模态的模型**。

作者试图用以下流程证明这个假设

1. 不同大模型表征底层数据的方式正在逐渐对齐；
2. 不同模态下，大模型衡量数据点距离的方法是非常相似的；
3. 这个收敛的方向，是向着一个通用的现实统计学模型。