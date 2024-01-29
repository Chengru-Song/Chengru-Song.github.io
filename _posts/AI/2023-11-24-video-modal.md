---
layout: article
title: 【AI】Scaling multimodal understanding to long videos
sidebar:
  nav: AI
aside:
  toc: true
key: llm
date: 2023-11-24 08:56:07 +0800
tags:
- 301-work-ai
category: [AI, AI_Basics]
typora-root-url: ../../../blog
mermaid: true
---

https://arxiv.org/pdf/2311.05698.pdf

AJ Piergiovanni，Google Research

# 场景与问题

解决不同模态之间的Heterogenious Input问题是一个重要课题。因为

1. 输入体积上，视频和音频在体积上比文字大得多，因此这两者Input信息量是无法对齐的，需要通过模型对齐。
2. 数据处理上，instruction following的Video QA模型的训练数据中包含提取出的文本信息（标题，简介等）是全局信息，而视频和音频都是和时间对齐的，其本身没有全局属性。

# 一般思路

1. Tokenize visual input - LLaVA



