---
layout: article
title: 【Best Practice】 Golang Microservice Structure
aside:
  toc: true
key: golang_ms_structure
date: 2022-03-07 10:40:40 +0800
tags:
- 301-work-blog
- 301-work-learning
category: [work, golang]
---
# Code Structure

```
.
├── Makefile          
├── README.md
├── app // 依赖注入
│   ├── container.go // 依赖注入容器
│   ├── wire.go      // wire 依赖声明
│   └── wire_gen.go  // wire 自动生成代码
├── build.sh         
├── conf // 项目配置               
│   ├── db.conf     // db配置
│   ├── db_boe.conf // boe db配置
│   └── kitex.yml   // server配置         
├── consts // 公用常量定义
│   ├── consts1.go // 以常量类型划分文件
│   └── consts2.go
├── dal // 数据访问层，所有外部数据依赖
│   ├── db // DB访问
│   │   ├── conn.go        // DB连接池
│   │   ├── table1.go      // 以表(或数据结构)的粒度划分文件
│   │   ├── table1_test.go // 对应DB访问单测
│   │   ├── table2.go
│   │   ├── table2_test.go
│   │   └── main_test.go   // 当前包单测的统一入口
│   ├── kv // kv访问，包括lc、redis、abase等
│   │   ├── main_test.go         // 当前包单测的统一入口
│   │   ├── abase_conn.go        // abase连接池
│   │   ├── redis_conn.go        // redis连接池
│   │   ├── redis_model1.go      // 以数据结构的粒度划分文件
│   │   └── redis_model1_test.go // 数据结构对应redis访问单测
│   ├── rpc // RPC访问
│   │   ├── article.go     // 以client粒度划分文件，每个文件实现各自client的构造方法
│   │   ├── params.go      // client构造的通用参数定义
│   │   └── user_packer.go
│   └── tcc // TCC配置访问
│       ├── conf.go     // 通用配置
│       └── demotion.go // 降级配置
├── go.mod
├── go.sum
├── handler.go
├── idl // 以submodule方式管理的公司统一idl库
├── kitex_gen // kitex生成代码
├── main.go
├── mock // go mock根据dal层interface生成代码
│   ├── db
│   │   ├── table1.go
│   │   └── table2.go
│   ├── kv
│   │   └── redis_model1.go
│   ├── rpc
│   │   ├── article
│   │   │   └── client.go
│   │   └── user_packer
│   │       └── client.go
│   └── tcc
│       ├── conf.go
│       └── demotion.go
├── model // 统一的model定义，包括db model、内部model等
├── operator // 数据操作层
│   ├── data
│   │   └── channel.go
│   ├── packer // 数据打包逻辑
│   │   ├── video_normal.go
│   │   └── video_parker.go
│   └── source // 数据loader，通过job manger实现
│       ├── article.go // article rpc的loader封装
│       ├── job_mgr.go // job mgr的定义及相关操作
│       ├── job_mgr_test.go
│       ├── source.go  // source常量及结果解析定义
│       └── user_packer.go
├── script // kitex自动生成脚本代码
├── service // 业务逻辑层
│   ├── business1 // 接口较多较复杂时，以业务模块划分二级目录
│   │   └── business.go
│   └── business2
│       ├── base.go        // 通用的业务处理逻辑
│       ├── create.go      // create业务逻辑
│       ├── create_test.go
│       ├── main_test.go
│       ├── mget.go
│       └── update.go
└── util // 通用工具，一般业务无关
    ├── convert.go // 以功能粒度划分文件，方便维护
    ├── convert_test.go
    ├── slice.go
    └── slice_test.go
```

# Concept Overview

## App Dependency Injection

What is dependency injection? Could refer to this link for a detailed look. [Dependency Injection - Wiki](https://en.wikipedia.org/wiki/Dependency_injection#:~:text=In%20software%20engineering%2C%20dependency%20injection,object%20is%20called%20a%20service.). The core idea behind **dependency injection** is to achieve seperation of concerns of construction and use of object. 

This picture shows how it is achieved. 

![img](https://upload.wikimedia.org/wikipedia/commons/1/10/W3sDesign_Dependency_Injection_Design_Pattern_UML.jpg)

There are several services that would be used by the client. It have to create the instances of other objects. With the help of *injector*, the client doesn't initiates `service A` and `service B` directly. Instead, the `Injector` will do the favor for the client. 

That's exactly what `wire` is doing in golang. `wire` is an injector for golang. The package will deduct the relationship between each service components and initiates them for you. For each service that needs to be initiated, create a service `NewXXXService` in the corresponding directory. 

It it worth noting that `wire` dependency injection framework **doesn't support initialize two identical object**. Refer to [Multipal bindings](https://github.com/google/wire/issues/206) for details. 

### Q&A

1. What should be initiated using dependency injection?
   Anything that depends on others or provide service for others. 
2. What object should be initiated manually instead of using dependency injection?
   Global database connections, global configurations, etc. 
3. What should I do when doing unit testing about non-dependency injection?
   Initiate them every time when you writing unit testing files. Specify testing db connections. 

## Makefile

A good makefile could release the labor in typing command lines endlessly. Refer to this file for details about makefile. 
