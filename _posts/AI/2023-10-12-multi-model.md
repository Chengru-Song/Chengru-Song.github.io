---
layout: article
title: 【AI】Multi-Modality Learning
sidebar:
  nav: AI
aside:
  toc: true
key: llm
date: 2023-10-12 20:50:07 +0800
tags:
- 301-work-ai
category: [AI, AI_Basics]
typora-root-url: ../../../blog
mermaid: true
---

# Problem Trying to Solve

提升大模型对多模态（语音，图像，视频，文本）的理解和推理能力，从而实现多模态理解和生成的能力。

# 解决方法

## LLaVA

1. 要解决的关键问题
   1. 构建一个有reasoning ability的，可以follow instruction的多模态模型。


2. 算法的根本思想
   1. 通过GPT-4构建训练集（包括与图片相关的对话、细节描述和复杂推理），使用了预训练的Vision Encoder(CLIP)把Image Token通过一个Projection matrix转化为一个文本长度的Embedding，并和文本的Embedding一起送入LLM得到prompt对应的结果（对话、描述和推理）。


3. 算法流程
   1. 


