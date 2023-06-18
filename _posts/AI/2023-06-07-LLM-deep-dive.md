---
layout: article
title: 【AI】LLM Deep Dive
sidebar:
  nav: AI
aside:
  toc: true
key: llm
date: 2023-06-07 16:46:07 -0700
tags:
- 301-work-ai
category: [AI, AI_Basics]
typora-root-url: ../../../blog
mermaid: true
---

# Overview

1. 什么是LLM？
2. LLM的Intuitive是什么？
3. LLM的原理是什么，底层是如何实现的？
4. 相比于其他方法，LLM为什么能够达到更好的效果？
5. LLM产业运行的难点在哪里？
6. 如果我现在起步，做和LLM什么相关工作比较好？机会点在哪里？
7. 如果有一个LLM相关工作的Roadmap，这个Roadmap是什么？
8. 如何与我现在的工作内容产生联系，让我更好起步？



因为我算是LLM领域的小白，所以我想从NLP的历史出发，看看如何一步步演变成目前的形态。

# Background

NLP的Intuitive是什么？为什么这种方法可行。

## Intuitive

### Statistical Model

参考这篇2001年的论文[A Bit of Progress in Language Modeling](https://arxiv.org/pdf/cs/0108005.pdf)， 统计模型主要解决的问题是，**一个单词序列出现的概率有多大**。即给定一个单词序列$\omega_1, \omega_2, ..., \omega_n$ ，这组单词序列出现的概率定义为$P(\omega_1...\omega_n)$，这个概率key被定义为一个组合概率：

$$P(\omega_1...\omega_n) = P(\omega_1) \times P(\omega_2 | \omega_1) \times P(\omega_3|\omega_1\omega_2) \times...\times P(\omega_n|\omega_1...\omega_{n-1}) $$ 

但是句子的长度是不固定的，对于任意长度的句子，如果都用这样的概率计算，那么这个计算量是非常大的，因此有n-gram的概念，即在某$n-1$个单词出现后，第$n$个是某个单词的概率是多大。

前两个词已经出现的情况下，第三个词出现的条件概率可以简化为在一个语料库中出现的频率。

$$P(\omega_i|\omega_{i-1}\omega_{i-2}) \approx \frac{C(\omega_i)C(\omega_{i-1})C(\omega_{i-2})}{C(\omega_{i-1})C(\omega_{i-2})}$$

这些概率模型有一些问题，比如光看统计特征，对于经常出现的或者只出现一次的词会有异常，因此引入了smoothing方法，这里不再赘述。

这些方法都有一个本质的问题，就是扩展这个序列的长度就能收获更好的效果，但同时扩展这个长度会增加计算量。因此如何获取到词与词之间的关系，尤其是上下文比较长的时候，对Statistic  Model来说是一个无法解决的问题。简称**the curse of dimensionality**。

### Neural Statistic Network Model

NLP统计学模型的dimensionality的本质是什么？假如语料库中有$n$个单词，其本质就是这$n$个单词的联立概率分布。即给定$i-1$个单词，模型可以找到下一个概率最大的单词。考虑到这个概率模型过于庞大，因此在2003年的文章[A Neural Probabilistic Language Model](https://jmlr.csail.mit.edu/papers/volume3/bengio03a/bengio03a.pdf)中提到可以使用神经网络将单词转化为一个特征向量，并用这个特征向量计算单词之间的概率分布。本质上是用神经网络来拟合这个联立概率方程。优点是这个特征向量是一个高密度向量，易于计算。

这个模型的输入是上下文转化后的dense feature vector，输出是和这个单词相关的下个词的概率。即，

$$f(\omega_t,..., \omega_{t-n+1}) = \hat{P}(\omega_t | \omega_{t-1}...\omega_{t-n+1})$$ 

因此在训练时，设置损失函数是输出函数的log likelihood，即最大化以下这个方程时的$\theta$.

$$L = \frac{1}{T}\sum_{t}\log f(\omega_t,\omega_{t-1},...,\omega_{t-n+1};\theta) + R(\theta)$$

神经网络结构如下图所示：

![image-20230616221548494](/assets/images/image-20230616221548494.png)
