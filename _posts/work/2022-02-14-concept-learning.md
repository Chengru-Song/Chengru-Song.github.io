---
layout: article
title: 【Big-Data】框架和概念学习
aside:
  toc: true
key: work_knowledge_archi&concept
date: 2022-03-01 14:20:08 +0800
tags:
- 301-work-blog
- 301-work-learning
category: [Blog]
---

# Concept Collection

| Category            | Concept         | Explanation                                                  | Pros                                                         | Cons                                                         |
| ------------------- | --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| General Platform    | Spark           | A unified analytics engine for large-scale data processing. <br />Spark: [Spark](https://en.wikipedia.org/wiki/Apache_Spark)<br />Spark Explanation: [Spark explanation](https://www.edureka.co/blog/spark-architecture/) | Spark is a fast  and resilient large distributed data processing platform.<br /> |                                                              |
|                     | Flink           | Flink is a framework and distributed processing engine for stateful computations over *bounded* and *unbounded* data. <br />Arch Intro: [Flink - Arch](https://flink.apache.org/flink-architecture.html)<br />Use cases: Event-driven applications, Data analytics applications, data pipeline applications. | Read event-log in a real time manner. The arch stores and processes data locally and update persistant remote storage periodically. In the meantime, it streams event to downstream. |                                                              |
|                     | Hadoop          | Hadoop is a framework the stores process and analyze data which are very **huge** in column. <br />Intro: [Hadoop](https://www.javatpoint.com/what-is-hadoop)<br /> |                                                              |                                                              |
| Storage             | HDFS            | HDFS is a distributed file system that stores very large files. | 1. Stores very large files.<br />2. Streaming data access(write-once, read-many-times)<br />3. Cheap hardware | 1. Low latency  data access.<br />2. Lots of small files<br />3. Multiple writes |
| Resource Scheduling | Yarn            | Yarn - Yet Another Resourse Negotiator. <br />Yarn provides a generic and flexible framework to administer the computing resources in the Hadoop cluster. |                                                              |                                                              |
|                     | Mesos           | Mesos is a platform for sharing commodity clusters between multiple diverse cluster computing frameworks, such as Hadoop and MPI. <br />Multiplexing a cluster between frameworks is the main use case of mesos. <br />White paper: [Paper](https://people.eecs.berkeley.edu/~alig/papers/mesos.pdf) | It could perform fine-grained resource sharing across diverse clustering computing frameworks. |                                                              |
| Data Analysis       | Pig             | Pig is an SQL-like language that could be compiled to a series of Map-reduce operations that could be optimized to execute. |                                                              |                                                              |
|                     | Hive            | The Apache Hive ™ data warehouse software facilitates reading, writing,  and managing large datasets residing in distributed storage using SQL. |                                                              |                                                              |
|                     | Kylin           | Apache Kylin™ is an open source, distributed Analytical Data Warehouse  for Big Data; it was designed to provide OLAP (Online Analytical  Processing) capability in the big data era. | Could be used in OLAP, near realtime.                        |                                                              |
|                     | Spark SQL       | Spark SQL could query structured data inside Spark programs, using eigher SQL or other programming languages such as Python, Java. etc. <br />More: [Spark](https://spark.apache.org/sql/) |                                                              |                                                              |
|                     | Spark Dataframe | A Dataframe is a dataset organised into named columns. It is conceptually equivalent to a table in a relational database or a data frame in python, but with a richer optimization under the hood. <br />Reference: [Spark Dataframe](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) |                                                              |                                                              |
|                     | Impala          | Impala is an Apache native arch that circumvents MapReduce operations to directly access the data through a specialized distributed data query engine. <br />Reference: [Impala](https://impala.apache.org/overview.html) |                                                              |                                                              |
|                     | Elastic Stack   |                                                              |                                                              |                                                              |

