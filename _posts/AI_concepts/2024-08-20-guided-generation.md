---
layout: article
title: 【AI】No free launch：Guided Generation 严重降低推理能力
sidebar:
  nav: AI
aside:
  toc: true
key: llm
date: 2024-08-20 09:10:07 +0800
tags:
- 301-work-ai
category: [ai_concepts, ai_inference, AI, AI_Algorithms]
typora-root-url: ../../../blog
mermaid: true
---

# No free lunch: Guided Gen严重降低推理能力

> 1. 类别：推理
> 2. 一句话总结：Guided generation可以强制让模型生成固定格式的输出，但是会严重降低推理能力。一个不错的解决方法是，采用两步策略，先让模型生成随意的output，再交互一轮生成带格式的输出。

## Existing Gap

Guided Generation能够强制模型生成固定格式，具体原理可以参考这篇文章：[https://hypercool.cn/ai_concepts/ai_inference/ai/ai_algorithms/2024/08/19/guided-decoding.html](https://hypercool.cn/ai_concepts/ai_inference/ai/ai_algorithms/2024/08/19/guided-decoding.html)。但是Guided Generation有什么问题吗？可以在下图看到JSON-mode在以下的benchmark上性能下降严重。

<img src="/assets/images/bbf7aad4f78f3dfa62be07fcaafafe85_1_Figure_2_1307511317.png" alt="bbf7aad4f78f3dfa62be07fcaafafe85_1_Figure_2_1307511317" style="zoom:50%;" />

## Proposed solution

目前的方法基本都是时间和空间换质量，简单来说，就是通过两步生成，第一步让模型按照生成要求自由发挥，再进行第二轮交互，让模型给原输出加一个固定的Format。第二步既可以用自然语言instruction的方式加format，也可以用guided Generation严格让模型输出固定格式。这两种方法都可以 提升结果的质量，同时保证format。

这里还有trick，就是第二步生成可以不用原始模型，而是使用更小的模型，因为第二步的任务只是加format，相对简单，可以极大缩短生成时延。

### 相关论文

SKCD（Sketch guided constrained decoding）：本质就是上面的交互流程，只是换了个更好的说法，sketch就是没有任何format constraints的输出，第二步用更小的Model来做formatting。

<img src="/assets/images/image-20240820113147001.png" alt="image-20240820113147001" style="zoom:50%;" />

从结果来看，SketchGCD在各个模型和方案上，都取得了较好的成绩。![image-20240820120239995](/assets/images/image-20240820120239995.png)

## Conclusion

用两步生成完成format的方式，能最大程度上缓解模型的生成质量下降的问题，且保证了格式化输出。