---
layout: article
title: 【AI】大模型训练方法汇总
sidebar:
  nav: AI
aside:
  toc: true
key: llm
date: 2024-08-26 20:34:07 +0800
tags:
- 301-work-ai
category: [AI, AI_Algorithms, Vision]
typora-root-url: ../../../blog
mermaid: true

---

# MiniCPM-V：端侧图像大模型

> - 标题：MiniCPM-V: A GPT-4V Level MLLMonYourPhone
> - 时间：2024.8.03
> - 作者团队：面壁智能
> - 作者：[Yuan Yao](https://arxiv.org/search/cs?searchtype=author&query=Yao,+Y), [Tianyu Yu](https://arxiv.org/search/cs?searchtype=author&query=Yu,+T), [Ao Zhang](https://arxiv.org/search/cs?searchtype=author&query=Zhang,+A), [Chongyi Wang](https://arxiv.org/search/cs?searchtype=author&query=Wang,+C), [Junbo Cui](https://arxiv.org/search/cs?searchtype=author&query=Cui,+J)
> - 有用指数：⭐️⭐️⭐️⭐️⭐️
> - 贡献程度：⭐️⭐️⭐️
> - 简单评价：主攻低参数大模型领域，提出了一个基于图片分片和压缩的处理方法，属于deep-fusion的一类模型。但训练过程较为复杂，光pretrain就分了三个训练阶段。