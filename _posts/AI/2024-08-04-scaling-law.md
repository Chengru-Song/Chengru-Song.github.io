---
layout: article
title: 【AI】Pretrain的Scaling law是什么
sidebar:
  nav: AI
aside:
  toc: true
key: llm
date: 2024-07-28 14:49:07 +0800
tags:
- 301-work-ai
category: [AI, AI_Algorithms, Vision]
typora-root-url: ../../../blog
mermaid: true
---

# Scaling Law研究，到底指的是什么？

可能很多研发在做模型的应用和与业务结合的部分，这部分内容往往涉及到模型在新业务场景的对齐调优（Alignment Tuning）。对齐调优一般有两类，一类是SFT，另一类的Preference对齐，可能使用DPO或者PPO。前者和预训练的区别不大，只是把数据改成了instruction+output的形式，后者需要构造pair-wise结果对比的数据。但这两者的共同特征是，**都不需要较大规模的训练的数据**，往往很少的数据就能在业务表现上提升一个层次。

Scaling law则更关注在预训练的过程中，应该如何平衡计算资源，数据量大小和模型大小之间的平衡，从而能在预训练之前就对成本和效果有预估，这样才有信心投入更多的训练资源，达到SOTA效果。

> [!NOTE]
>
> 1. 如果有人说，自己在做Scaling law研究，他们做的事情是什么？
>
> 大概率他们在做一个新的大模型，比如一个自研的大语言模型，更大概率他们在做一个新的架构下的大模型，因为LLM的Scaling Law OPENAI已经研究的比较好了，基本上在Decoder-only的架构下训练大语言模型结论已经明确了，除非有些corner-case是需要为LLM加入新的特性，导致原本可以收敛但无法收敛，此时也需要继续对scaling law有扩展。

## Scaling Law是什么

从根本上说，Scaling Law试图回答大型语言模型的规模化规律问题：在以 FLOPs（浮点运算）衡量的有限计算预算下，模型规模和训练数据规模（以标记数量衡量）的何种最优组合会产生最低的Loss？ 

根据OpenAI的研究结论是：
