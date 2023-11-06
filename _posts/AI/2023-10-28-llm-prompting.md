---
layout: article
title: 【AI】LLM Prompting
sidebar:
  nav: AI
aside:
  toc: true
key: llm
date: 2023-10-28 16:20:07 +0800
tags:
- 301-work-ai
category: [AI, AI_Basics]
typora-root-url: ../../../blog3
mermaid: true
---

# Background

要解决AIGC业务落地的问题，在做特别hardcore的事情之前，至少有三个方向可以考虑。

1. Prompting，直接给LLM写好prompt，通过few-shots，CoT等技巧，直接让GPT生成结果。
2. Agent，设定一个目标，让GPT通过CoT生成Task，解决Task等方法最终直接解决问题。
3. SFT，直接在产出结果后面加一个layer，fine-tune一下，加上1和2的一些方法，能否达到预期的效果。

# Prompt Engineering

## Roadmap

[Roadmap](https://roadmap.sh/prompt-engineering)

![image-20231028162648178](/../blog/assets/images/image-20231028162648178.png)