---
layout: article
title: 【AI】LLM Learning
sidebar:
  nav: AI
aside:
  toc: true
key: llm
date: 2023-11-03 22:56:07 +0800
tags:
- 301-work-ai
category: [AI, AI_Basics]
typora-root-url: ../../../blog
mermaid: true
---

# Pretrain

## Performance v.s. Data & Size

> For a given compute budget, the best performances are not achieved by the largest models, but by smaller models trained on more data. -- from LLaMA

用更多的数据训练，Size小一点也会有更好的效果。

LLaMA

Encoding: BPE

Training Data: 1.4T token, Wikipedia和Books Domain训练了两个epochs

Epoch meaning:

> [In the context of machine learning, an epoch is one complete pass through the training data](https://deepai.org/machine-learning-glossary-and-terms/epoch)[1](https://deepai.org/machine-learning-glossary-and-terms/epoch). It is typical to train a deep neural network for multiple epochs, meaning that the same data is used repeatedly to update the model’s parameters.



