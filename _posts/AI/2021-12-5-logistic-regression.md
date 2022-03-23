---
layout: article
title: Logistic Regression详解
sidebar:
  nav: AI
aside:
  toc: true
key: logis
tags:
- 301-work-ai
- 301-work-basics
- 301-work-interview
mathjax: true
category: [AI, AI_Basics]
---
# Logistic Regression

Advantage: Use logistic function
Usage: Used to predict classification problems

# Introduction

Logistic regression is used to predict classification result

# Details

Suppose we try to predict whether an email is spam, we have attributes $x_1, x_2, ... x_n$ for prediction. 

We define a function $z = \theta_0 + \theta_1x_1 + \theta_2x_2+...$, in which $z$ is a middleware. The final result is presented as $h(z) = \frac{1}{1+e{-z}}$, which is sigmoid function. 

In order to train a this regression model, we should have a cost function to measure the distance between predicted value and actual value.

Cost function is logistic regression is defined as:

$$\begin{equation}
C(z) = -\frac{1}{m}\left(\sum_{i=1}^m y^{(i)}\log h_\theta(x^{(i)}) + (1-y^{(i)}) \log (1-h_\theta(x^{(i)})\right)
\end{equation}$$

In order to optimize this function, we need to calculate the gradient of cost function. 

The gradient of sigmoid function $h$ is $h-h^2$, so the calculation will be 

$$\begin{equation}
\frac{\partial C}{\partial \theta} = \frac{1}{m} \cdot x^{(i)} \cdot \sum_{i=1}^m h_i-y_i
\end{equation}$$

# Code

```python
y = theta_0 + theta_1 * x_1 + theta_2 * x_2 + theta_3 * x_3 + theta_4 * x_4
y = sigmoid(y)
    
    cost = (- np.dot(np.transpose(y_train),np.log(y)) - np.dot(np.transpose(1-y_train),np.log(1-y)))/m
    
    theta_0_grad = np.dot(np.ones((1,m)),y-y_train)/m
    theta_1_grad = np.dot(np.transpose(x_1),y-y_train)/m
    theta_2_grad = np.dot(np.transpose(x_2),y-y_train)/m
    theta_3_grad = np.dot(np.transpose(x_3),y-y_train)/m
    theta_4_grad = np.dot(np.transpose(x_4),y-y_train)/m
    
    theta_0 = theta_0 - alpha * theta_0_grad
    theta_1 = theta_1 - alpha * theta_1_grad
    theta_2 = theta_2 - alpha * theta_2_grad
    theta_3 = theta_3 - alpha * theta_3_grad
  theta_4 = theta_4 - alpha * theta_4_grad
    
    cost_func.append(cost)
  epochs += 1
```