---
layout: article
title: 【Basics】Git Rebase
aside:
  toc: true
key: git_rebase
date: 2022-12-01 09:18:07 +0800
tags:
- 301-work-blog
- 301-work-learning
category: [work, git]
typora-root-url: ../../../blog
---

# Git rebase

## When to use

If you want to have a clean git commits history. If six features are being developed in parallel, they starts from different time and they launch in different times. When you need to roll back some features, you will discover that you have diverged from the main so much. 

## What is git rebase

![feature branch](https://resources.jetbrains.com/help/img/idea/2022.2/feature_branch_diagram.png)

![feature branch diverged from master](https://resources.jetbrains.com/help/img/idea/2022.2/feature_branch_diverge_from_master_diagram.png)

![rebase operation result](https://resources.jetbrains.com/help/img/idea/2022.2/rebase_result_diagram.png)

When you merge a branch, step 3 will actually look like this:

![merge result](https://resources.jetbrains.com/help/img/idea/2022.2/merge_result_diagram.png)

