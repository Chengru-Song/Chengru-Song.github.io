---
layout: article
title: 【Basics】Useful Prompts
aside:
  toc: true
key: promot
date: 2023-04-18 14:44:07 +0800
tags:
- 301-work-blog
- 301-work-learning
category: [work, basics]
typora-root-url: ../../../blog
---

# Prompt Techniques



# Specific Prompts

## Scala Prompts



1. Incremental Id assignment
   1. suppose I have a rdd called edgeDF which has src_id, dst_id columns. Now I want to turn srd_id and dst_id into a single column called vertex_id. Each vertex_id is unique and assign a new column called id with incremental number. 
      Finally, I want to assign the id column to src_id and dst_id in the original edgeDF. please write the scala program