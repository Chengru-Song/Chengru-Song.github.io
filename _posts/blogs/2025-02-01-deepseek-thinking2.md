---
layout: article
title: 【Blog】Deepseek没写的技术细节 - part2
aside:
  toc: true
key: work_reflection
date: 2025-02-01 22:46:07 +0800
tags:
- 301-work-blog
category: [work, Blog]
typora-root-url: ../../../blog
---

# DeepSeek R1的细节更正

## 1. 通用任务的Reward是否有区别？

确实是存在的，原文中有提到，复用了在Deepseek-v3的Pipeline中的Reward。

首先，R1的训练过程如下

1. **==SFT冷启动==**：使用**数千条**（具体数量文中没有提及）训练Deepseek-V3-Base，这几千条数据的特征是，few-shot prompting with a long CoT，使用Reflection和Verification生成。主要提升**可读性和RL潜力**。

2. **==Reasoning RL训练==**：使用RL对有明确反馈信号的任务进行训练，主要是Coding，math，science和logic reasoning。因为这些问题是**有明确对错Reward**的任务。通过Long CoT解决。**这步没有Format Reward！**没有Format Reward原因也很简单，SFT之后不太会有Format问题。有的Reward是两个，①对错Reward；②语言一致性Reward（语言不一致给个地方）。直接相加作为最终Reward。
3. **==拒绝采样SFT==**：数据两部分组成，reasoning+Knowledge。Reasoning扩展数据量级到**600k**，任务从之前明确对错问题**扩展到通用任务**。数据用第2步的ckpt过滤了CoT有问题的数据+Deepseek-V3的生成式Reward模型过滤了答案有问题的数据，最终生成的数据对模型做了SFT。Knowledge数据量级**200k**，但对于其中部分数据（量级不详）构造了CoT。
4. **==全量RL==**：reasoning数据继续使用rule-based Reward，通用类回答复用了Deepseek-V3的Pipeline构造出了同分布的preference pairs。



## 2. R1可能是怎么训练出来的？

考虑到R1-zero的一些设计和最终R1的结果，我推测过程可能如下：

1. Deepseek想最开始直接做纯RL的模型，想办法提升模型的Reasoning能力，就提出了R1-Zero，用Format Reward + Accuracy Reward直接训练出来一个模型；
2. 随后发现出现了Aha moment，模型能自己reason了，但是说话能力比较差；常见的提升说话能力的就是直接SFT，做Next token prediction训练；
3. 随后搞了RL + SFT，大概率同时发生了两件事，①说话能力提升明显，但是reasoning能力下降更明显；②保持住部分reasoning能力，但是说话能力救不回来了。
4. 后面再训练的目标非常明确：**保住Reasoning Aha moment的同时，让模型能正常说话**。所以干脆把SFT放到前面，但是只用少量数据，先保证模型能正常说话，再训练Reasoning能力，相当于回到之前的工作流里面，但由于第一步已经有了Aha moment，第二步RL的数据是不需要再组织的。
5. 个人猜测这两步之后的流程就是想办法加入通用数据，把其他任务的Benchmark刷的好看点，加上有之前SFT时候构造Reasoning SFT的经验，直接把这个复用到通用任务上。但我觉得这步的耗时可能非常多，因为这是一个最后一公里任务，还是比较难的。
6. 最后一步RL是通用的，任何模型都会做的事情，但由于之前的RL已经产生了Aha moment，给模型的CoT带来了泛化性，所以在其他通用任务上，这个CoT + SFT出现了奇效。
7. 引用下上篇博文的其他大佬的总结：这个工作本质验证的是，让一个Reasoner变成Generalist，而不是反过来。但启动得用500B+基座。但是否完全需要500B+的基座启动R1-zero，也不一定。因为这几天已经有很多复现工作了，用的也不是500B+的大模型，也都是10B以下的小模型，所以让子弹再飞一会。
8. 下一篇会整理下现在比较有名的复现R1或者R1-Zero的工作，感兴趣的话可以关注一波看后续。
