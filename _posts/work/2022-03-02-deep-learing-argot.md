---
layout: article
title: 【AI】Deep Learning Argot
aside:
  toc: true
key: work_knowledge_archi&concept
date: 2022-03-02 12:45:31 +0800
tags:
- 301-work-blog
- 301-work-learning
category: [work]
---

# Argot Definition

| Argot                | Explanation                                                  |
| -------------------- | ------------------------------------------------------------ |
| Expressivity         | 模型的表达能力用来衡量参数化模型如神经网络的可以拟合的函数的复杂程度。深度神经网络的表达能力随着它的深度指数上升， 这意味着中等规模的神经网络就拥有表达监督， 半监督， 强化学习任务的能力[2]。 深度神经网络可以[记住非常大的数据集](https://arxiv.org/abs/1611.03530)就是一个很好的佐证。 |
| Generalization       | 泛化能力，推广模型到不同的场景中的能力，表达能力增强，则能力下降。因为表达能力代表了当前场景下的对特定问题的表达程度。 |
| Regularization       | 正则化，为了防止过拟合而引入在目标函数的公式中，这其中有$$L_1$$, $$L_2$$, $$L_0$$正则项，他们都分别为了解决不同的问题而在不同的情况下使用。 |
| Deep & Shallow Model | 针对不同的机器学习方法，使用了CNN，DNN的是Deep model，使用了传统的机器学习方法，例如各种回归，一般来说都是shallow model。 |

