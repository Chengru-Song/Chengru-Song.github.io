---
layout: article
title: 【Golang】Service mesh详解
sidebar:
  nav: BASICS
aside:
  toc: true
key: service-mesh-01
tags:
- 301-work-service-mesh
- 301-work-basics
- 301-work-interview
category: [work, archi, interview]
---

# Definition

Service Mesh是一个可配置的、低时延的架构层应用，主要应用在微服务框架中，用来处理内部微服务的海量数据通信的一种主要基于应用层API编写的框架。整体可以参考下图：

![Image](/assets/images/service_mesh.svg)

我们从用户发起请求到请求被返回的流程对service mesh负责的各个功能和模块进行拆解。

1. Container Orchestration Framework
这是一个系统引擎，当外部请求进入到系统内部时，首先访问到整个Framework，这个Framework能够对微服务框架的所有功能模块进行全局和局部的配置以及管理，并在这个基础上提供不同粒度的功能，例如维护服务发现表，进行内部网负载均衡等。
2. Load Balancing
当有大量流量进入到系统内部时，该功能模块从transport layer和application layer两层进行负载均衡，在入口处有相关的负载均衡算法保证系统的稳定性。
3. Service Discovery
我们知道在微服务框架内部，每一个微服务会负责一个基础功能，这些基础功能会以链式的形式进行访问，在部署这些微服务时，由于他们粒度足够小，所以会经常出现部署新服务，删除旧服务的操作。所以框架提供了一个全局服务发现表，用来记录当前共有哪些微服务。
4. Sidecar Proxy
当请求找到了需要访问的微服务，微服务内部是由很多pod组成的，每个pod可以是一个虚拟化的容器或者是主机，我们通过一个sidecar proxy对每个微服务进行管理，例如其中的pod的主机可能处于不同地理位置，sidecar相当于提供了一个虚拟层，把这些物理主机通过proxy连接在一起，组成一个微服务。
5. Encryption
在不同微服务间进行请求链时可以在入口处进行加解密，而在后续微服务不需要进行类似操作，减轻了负担。
6. Authentication & Authorization
身份验证和权限验证是非常重要的一部分，service mesh能够帮助对内部请求和外部请求进行这两个操作，保证框架的安全性。