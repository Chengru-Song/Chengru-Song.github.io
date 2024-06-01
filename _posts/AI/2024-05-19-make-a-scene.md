---
layout: article
title: 【AI】Make A Scene - Meta的T2I核心解析
sidebar:
  nav: AI
aside:
  toc: true
key: llm
date: 2024-05-19 16:37:07 +0800
tags:
- 301-work-ai
category: [AI, AI_Algorithms]
typora-root-url: ../../../blog
mermaid: true
---

# Meta T2I方案解析

> - 时间：2022.3.24
> - 作者团队：FAIR at Meta（Meta的AI研究团队）
> - 作者：Oran Gafni, Adam Polyak, Oron Ashual, Shelly Sheynin, Devi Parikh, Yaniv Taigman
> - 有用指数：⭐️
> - 贡献程度：⭐️
> - 简单评价：

原文标题：[Make-A-Scene: Scene-Based Text-to-Image Generation with Human Priors](https://arxiv.org/abs/2203.13131)  

## Existing Gap

1. **生成细节的可控性很低**：有些控制能精准被语言描述，但有更多是很难被语言描述的，后者的控制非常难。

2. **感知差异**：生成人脸时候优化的目标和结果之间有Gap，导致生成目标和实际结果差异很大
3. **图片分辨率太小**：当前SoTA还是256x256分辨率大小的。他们想把这个Size增大到512x512。

