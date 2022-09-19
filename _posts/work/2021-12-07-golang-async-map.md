---
layout: article
title: 【Golang】Golang Map并发编程
sidebar:
  nav: BASICS
aside:
  toc: true
key: golang_map_async
tags:
- 301-work-golang
- 301-work-basics
- 301-work-interview
category: [work, golang]
---

# Golang Map并发读写

## 直接并发读写

如果直接对`map`进行并发读写，那么会造成Panic或者error，如下所示：

```go
package main

func main() {
  m := make(map[int]int)
  go func() {
    for {
      read := m[0] // sync read
    }
  }()

  go func() {
    for {
      m[0] = 1 // sync write
    }
  }()
}
```

上面程序有问题

## 传统加锁读写

```go
package main

func main() {
  m := make(map[int]int)
  mc := sync.Mutex

  go func() {
    for {
      mc.Lock()
      read := m[0]
      mc.Unlock()
    }
  }()

  go func() {
    for {
      mc.Lock()
      m[0] = 1
      mc.Unlock()
    }
  }()
}
```

加锁读写。

## Go1.9之后sync.map

```go
package main

func main() {
  m := sync.Map

  go func() {
    for {
      v, ok := m.Load(1)
    }
  }()

  go func() {
    for {
      m.Store(1, 2)
    }
  }()
}
```

上述程序都是先写后读。
