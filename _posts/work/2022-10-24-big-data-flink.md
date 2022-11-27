---
layout: article
title: 【Big-Data】Flink开发总结
aside:
  toc: true
key: flink_development
date: 2022-10-24 09:18:07 +0800
tags:
- 301-work-blog
- 301-work-learning
category: [work,java] 
typora-root-url: ../../../blog

---
# Flink开发（Java版）

## 1.1 Data Conversion

Reference: [Flink DataStream /DataSet 与Table的互相转化_唐予之_的博客-CSDN博客](https://blog.csdn.net/lxhandlbb/article/details/83304153)

### 1.1.1 Imports

```java
import org.apache.flink.streaming.api.scala.
import org.apache.flink.table.api.scala._
import org.apache.flink.api.scala._
import scala.collection.JavaConverters._
```

### 1.1.2 Register Datastream of DataSet as Table

```java
// get TableEnvironment
// registration of a DataSet is equivalent
val tableEnv TableEnvironment.getTableEnvironment(env)
val stream:Datastream[(Long,String)]=...
//register the Datastream as Table "myTable"with fields "fo","f1"
tableEnv.registerDataStream("myTable",stream)
//register the Datastream as table "myTable2"with fields "myLong","mystring"
tableEnv.registerDatastream("myTable2",stream,'myLong',myString)
```

### 1.1.3 Convert a DataStream or Dataset into a Table

```java
//get TableEnvironment
//registration of a DataSet is equivalent
val tableEnv TableEnvironment.getTableEnvironment (env)
val stream:Datastream[(Long,String)]=..
//convert the Datastream into a Table with default fields '_1,'_2
val table1:Table tableEnv.fromDatastream(stream)
//convert the Datastream into a Table with fields 'myLong,'myString
val table2:Table tableEnv.fromDataStream(stream,'myLong,'myString)
```

### 1.1.4 Convert a table into a DataStream

```java
//get TableEnvironment.
//registration of a DataSet is equivalent
val tableEnv TableEnvironment.getTableEnvironment(env)
//Table with two fields (String name,Integer age)
val table:Table =..
//convert the Table into an append Datastream of Row
val dsRow:DataStream[Row]tableEnv.toAppendStream[Row](table)
//convert the Table into an append Datastream of Tuple2[String,Int]
val dsTuple:DataStream[(String,Int)]dsTuple
tableEnv.toAppendstream[(String,Int)](table)
//convert the Table into a retract Datastream of Row.
/1
//A retract stream of type X is a Datastream[(Boolean,X)].
//
//The boolean field indicates the type of the change.
//
//True is INSERT,false is DELETE.
val retractstream:Datastream[(Boolean,Row)]=tableEnv.toRetractstream [Row](table)
```

### 1.1.5 Convert table into a DataSet

```java
//get TableEnvironment
//registration of a DataSet is equivalent
val tableEnv = TableEnvironment.getTableEnvironment (env)
//Table with two fields (String name,Integer age)
val table:Table =..
//convert the Table into a DataSet of Row
val dsRow:DataSet[Row] = tableEnv.toDataSet [Row](table)
//convert the Table into a DataSet of Tuple2[String,Int]
val dsTuple:DataSet[(String,Int)]=tableEnv.toDataSet[(String,Int)](table)
```

