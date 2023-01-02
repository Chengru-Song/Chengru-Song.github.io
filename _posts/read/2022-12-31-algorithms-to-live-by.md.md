---
layout: article
title: 【阅读】Algorithms to Live By
aside:
  toc: true
key: 2022_threads
date: 2022-12-31 13:41:58 +0800
tags:
- 301-life-blog
category: [Blog, Read] 
typora-root-url: ../../../blog
---

# Algorithms to live by

我觉得很多人应该更熟悉这本书的中文译名，算法之美。看到了微信读书上对于这个译名的评价：为了追求所谓的“信达雅”而没有正确翻译出题目想表达的主题，乍一看还以为是讲算法的，实际上是讲算法在日常生活中的应用的。并且后半部分的翻译好像是为了赶工，看上去并没有针对中国人的表达习惯进行优化，非常像是机翻的，这就导致了后面几章的内容读起来比较晦涩。因此为了避免这个情况，我直接选择读原版的英文版。这反而对于我比较友好，因为上学学算法的时候就是英文教材，很多argot是可以直接代入和理解的，比阅读中文版更能让我理解作者表达的原意。

Regardless of that，这本书讲的还是非常好的。通过算法引申到哪些现实问题其实是这个算法的真实映射，而如果算法本身能够提供解决问题的最优解，那么我们同样可以把这个策略用到我们的生活当中。

## Optimal Stopping

一个具体问题，如果现在作为一个面试官，要找到合适的候选人，那么我们应该如何做能尽量确保我们失误的概率更低呢？



## Scheduling
### 基本概念

1. DDL优先：当你的事情没有重要性排序的时候，你只需要将每一个事情按照到达次序处理，找到快要DDL的事情来做就行。
2. Thrashing，当你的事情有优先级排序的时候，很多事情又同时在做，就会崩溃，你发现其实一直在做context swtich，最后其实什么也没做，在外界看来，这样甚至成了拖延症。
3. 最小时间分片。操作系统实际上有个最小分片，小于这个分片，系统除了不断做context swtich，什么计算任务也无法完成，所以这个最小时间分片是无法继续分割的，必须做完才能做另一个任务。

### Takeaway

1. 确定自己的最小处理时间分片。在一个分片内，尽量只做一件事。“The moral is that you should try to stay on a single task as long as possible without decreasing your responsiveness below the minimum acceptable limit.”
2. Interrupt coalesce. 把给你的中断尽可能合并，比如统一回复邮件。
3. 对于概念2，可以随机处理事情，而不需要一直卡着优先级排序处理，否则在计算优先级这里又需要花很多时间。