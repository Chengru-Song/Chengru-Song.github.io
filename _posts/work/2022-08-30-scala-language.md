---
layout: article
title: 【Big-Data】Scala
aside:
  toc: true
key: scala_basics
date: 2022-08-30 11:18:07 +0800
tags:
- 301-work-blog
- 301-work-learning
category: [work, scala, basics]
typora-root-url: ../../../blog
---

# What is Scala

Scala is used in **Big Data Processing**, **Machine Learning**, **Streaming Services**, etc. It is designed to run big data applications based on Java. If your application requires big data processing which contains logs of SQL-like quries, and those query results needs some further processing according to some business logic implementations, it works for you. 

With regard to **Big Data Processing**, check [spark v.s. flink](https://www.infoq.cn/article/spark-vs-flink) for details. 

![image-20220830154156667](/assets/images/image-20220830154156667.png)

# How to use Scala

Reference docs: 

1. [Get started](https://docs.scala-lang.org/getting-started/index.html)
2. [Build and test with sbt](https://docs.scala-lang.org/scala3/book/tools-sbt.html)



## Basic tools and usages

| Commands      | Descrpition                                                  |
| ------------- | ------------------------------------------------------------ |
| `scalac`      | scala compiler                                               |
| `scala`       | scala script runner                                          |
| `scala-cli`   | interactive console for scala                                |
| `sbt`, `sbtn` | sbt build tool, doc see here: [build and test with sbt](https://docs.scala-lang.org/scala3/book/tools-sbt.html) |
| `amm`         | enhanced REPL                                                |
| `scalafmt`    | code formatter                                               |
| `cs`          | Coursier, similiar to Maven, a dependency management tool    |

## Steps

1. Create a template project by running `sbt new scala/scala3.g8`. 
2. 