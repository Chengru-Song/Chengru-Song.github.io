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
   1. 构建一个有reasoning ability的，可以follow instruction的多模态模型；
   2. Instruction following的MultiModal数据的缺失；
   3. 如何使用现有大模型把这几个功能融合在一起。


2. 算法的根本思想
   1. ==通过GPT-4 Prompting构建训练集（包括与图片相关的对话、细节描述和复杂推理）==，使用了预训练的Vision Encoder(CLIP)把Image Token通过一个Projection matrix转化为一个文本长度的Embedding，并和文本的Embedding一起送入LLM得到prompt对应的结果（对话、描述和推理）。
   2. Model Architect
      1. ![image-20231107102944935](/assets/images/image-20231107102944935.png)

3. 算法流程
   1. Pre-train alignment
      1. LLM和Vision Encoder的weights都是frozen的，只学习Vision Encoder到LLM Token的projection matrix

   2. Fine-tune到各种下游任务
      1. visual Encoder的weights frozen，LLM和Projection Matrix的weights更新以适应成为Chatbot等。还有一些为了刷榜做的adaption。


4. Experiments
   1. ![image-20231107104253614](/assets/images/image-20231107104253614.png)
   2. Ablations
      1. 用了不同版本的CLIP，最好的和最新的有0.96的差距，作者分析原因是现在的CLIP last layer更多关注图片的Global information，所以和他的前代相比，在描述图片细节的时候会有一些差距![image-20231107104739506](/assets/images/image-20231107104739506.png)
5. Take aways
   1. LLaVA使用的prompts![image-20231107104925859](/assets/images/image-20231107104925859.png)
   2. LLaVA如何在Prompts里面用images做few-shot:https://github.com/haotian-liu/LLaVA/blob/6944051041cd1ab3b68fd5c7920153d6cc1824a8/llava/mm_utils.py#L43
      1. 在Prompts里面放base64编码的image
      2. 取出image并Tokenize送给大模型![](/assets/images/2023-11-07-12-12-09.png)
