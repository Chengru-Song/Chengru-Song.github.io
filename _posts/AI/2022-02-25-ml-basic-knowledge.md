---
layout: article
title: 【AI】ML Basic Knowledge
sidebar:
  nav: AI
aside:
  toc: true
key: ml_basic_knowledge
tags:
- 301-work-ai
- 301-work-basics
category: [AI, AI_Basics]
---
# Common Metrics

| Name          | Explanation                                                                                                                                                                                                                                                                             | Key Params                                  | Usage                                                                                                                                                                                                                                                                                                                                  | Example                                                                                                                                                                    |
| ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ROC Curve[^1] | Receiver operating characteristic curve: A graph showing the performance of a**classification** model at all classification threshhold.                                                                                                                                                 | y: True Positive Ratex: False Positive Rate | ROC curve is used to indicate the                                                                                                                                                                                                                                                                                                      | ![ROC Curve showing TP Rate vs. FP Rate at different classification thresholds.](https://developers.google.com/machine-learning/crash-course/images/ROCCurve.svg?hl=zh_cn) |
| AUC Curve[^2] | Area under the ROC curve: One way of interpreting AUC is as the probability that the model ranks a random positive example more highly than a random negative example.when randomly choose one sample, the probability that the score of predicted true label comes before false label. |                                             | ![Positive and negative examples ranked in ascending order of logistic regression score](https://developers.google.com/machine-learning/crash-course/images/AUCPredictionsRanked.svg?hl=zh_cn)AUC represents the probability that a random positive (green) example is positioned to the right of a random negative (red) example.[^1] | ![AUC (Area under the ROC Curve).](https://developers.google.com/machine-learning/crash-course/images/AUC.svg?hl=zh_cn)                                                    |
| TPR           | True positive rate, synonym for recall, actual positive data is classified as positive.                                                                                                                                                                                                 | $\frac{TP}{TP+FN}$                          |                                                                                                                                                                                                                                                                                                                                        |                                                                                                                                                                            |
| FPR           | False positive rate, negative data is classified as positive.                                                                                                                                                                                                                           | $\frac{FP}{FP+TN}$                          |                                                                                                                                                                                                                                                                                                                                        |                                                                                                                                                                            |

# Concepts

## Prediction and Recall

This is very well introduced in this article: [Precision and recall](https://en.wikipedia.org/wiki/Precision_and_recallhttps://).

In [pattern recognition](https://en.wikipedia.org/wiki/Pattern_recognition "Pattern recognition"), [information retrieval](https://en.wikipedia.org/wiki/Information_retrieval "Information retrieval") and [classification (machine learning)](https://en.wikipedia.org/wiki/Classification_(machine_learning)) "Classification (machine learning)"), **precision** and **recall** are performance metrics that apply to data retrieved from a [collection](https://en.wikipedia.org/wiki/Data_store "Data store"), [corpus](https://en.wikipedia.org/wiki/Text_corpus "Text corpus") or [sample space](https://en.wikipedia.org/wiki/Sample_space "Sample space").

![Image](/assets/images/2022-02-25-18-45-57.png)

The difference between precision and recall is that precision measures how accurate of a certain retrieval, which differs a lot when retrieving multiple times from database. On the other hand, recall measures how complete of a certain retrieval. 



[^1]: [Google ML Basics](https://developers.google.com/machine-learning/crash-course/classification/roc-and-auc?hl=zh_cn)

[^2]: [ROC curve](https://zhuanlan.zhihu.com/p/58587448)

