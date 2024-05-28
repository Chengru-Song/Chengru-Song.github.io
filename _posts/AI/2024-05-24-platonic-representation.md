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

该假设的具象理解，假设现实是Z，那么X和Y两个模态的模型学习出来的embedding表征的内容是一致的。

![image-20240524223441548](/C:/Users/26092/AppData/Roaming/Typora/typora-user-images/image-20240524223441548.png)

## 如何证明这个表征是收敛的

这里作者引入了一个*kernel*的概念。简单理解为模型区隔data points的本质特征。随后采用了*mutual nearest-neighbor metric*，用来衡量两个*kernel*之间的距离。作者试图证明两个东西

1. 不同的神经网络正在收敛；
2. 收敛的这个现象在不同的模态下都是成立的。

他们介绍了一个工作，叫做model stitching（模型缝合）。给定两个训练好的模型$f$和$g$​，假设他们都是由n层神经网络组成的，作者引入一个仿射层h，组成一个新的网络
$$ {stitching_layer}
F = f_1 \circ \cdots \circ f_k \circ h \circ g_{k+1} \circ \cdots \circ g_m
$$
如果这个新的网络$F$能有很好的表现，那么就证明$f$和$g$有能兼容的层。

他们的实验结果共有两个发现，

1. 一个在ImageNet上训练的视觉网络能和一个在Places-365上训练的网络进行对齐并保持较好的表现；
2. 更靠前的层比靠后的层可交换性更强。

