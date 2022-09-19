---
layout: article
title: 【AI】Term-Vector含义
sidebar:
  nav: AI
aside:
  toc: true
key: term_vector
tags:
- 301-work-ai
- 301-work-basics
- 301-work-interview
category: [AI, AI_Basics]
---
# Term-vector

It means that each word forms a separate dimension:

For a model containing only three words you would get:

```
dict = { dog, cat, lion }

Document 1
“cat cat” → (0,2,0)

Document 2
“cat cat cat” → (0,3,0)

Document 3
“lion cat” → (0,1,1)

Document 4
“cat lion” → (0,1,1)
```