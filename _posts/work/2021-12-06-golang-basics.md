---
layout: article
title: 【Golang】Golang语言基础（面经）
sidebar:
  nav: BASICS
aside:
  toc: true
key: golang-basics
tags:
- 301-work-golang
- 301-work-basics
- 301-work-interview
category: [work, golang]
---

# Basic Knowledge

## 声明

- 短变量声明只能在函数内部使用

```go
package main

// myvar := 1 // false
var myvar = 1 // right

func main() {
}
```

- 不能使用nil初始化未指定类型的变量

```go
var x = nil // false
var x interface{} = nil // right
_ = x
```

- 字符串没有nil

```go
var x string

if x == nil { // err

}
if x == "" {
  x = "default"
}
```

## Slice & Array

首先Slice和Array是有区别的，Slice是指针，而Array已经预分配好了空间，所以这两者会产生表现形式上的差异，但是都可以通过append函数添加到末尾。
```go
s0 := []int{1, 2, 3} // slice, not empty
var s1 []int // nil slice
s2 := make([]int, 0) // empty slice, but not nil
arr := [3]int{1, 2, 3} // array
```

- 数组在函数传参时传递的是复制，不是引用，slice在函数传递时是指针，所以会被改变，最大的区别在于只想的数组引用地址不同
![Image](https://mmbiz.qpic.cn/mmbiz_png/FmVWPHrDdnmEnIljHLiaRm4UQYQugoxmeN2PG36E45eGuuibRZQQQrnxAIb9EC67judR31DewqtrNiaw2nrs5rmDQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
![Image](https://mmbiz.qpic.cn/mmbiz_png/FmVWPHrDdnmEnIljHLiaRm4UQYQugoxmeN2PG36E45eGuuibRZQQQrnxAIb9EC67judR31DewqtrNiaw2nrs5rmDQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

空切片指向地址相同：
![Image](https://mmbiz.qpic.cn/mmbiz_png/FmVWPHrDdnmEnIljHLiaRm4UQYQugoxmeTsMB2g7bOhTvZmtR1BxqbRAMtPjZaLhUpHmMuItBiatVDCTLw1ejZxA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

```go
arr := [3]int{1, 2, 4}
var changeFirst = func(arr [3]int) {
  arr[0] = 7   // arr = {7, 2, 4}
}
changeFirst(arr)
fmt.Println(arr) // arr = {1, 2, 4}, will not change arr

slice := []int{1, 2, 3}
var changeFirst = func(arr []int) {
  arr[0] = 7   // arr = {7, 2, 4}
}
changeFirst(arr)
fmt.Println(arr) // arr = {7, 2, 4}, will change arr
```

- Slice和Array都是一维的，就算二维实际上也是一维的展开

## 字符串

- 字符串不可变，转换成[]byte数组时才可变

```go
x := "text"
// x[0] = 'T' // error

xBytes := []byte(x)
xBytes[0] = 'T' // no problem
```

- 字符串与[]byte之间的转换是复制，有内存消耗，有两种方法可以做到
  1. map[string][]byte建立映射
  2. 使用range避免内存分配

```go
stringToByte := make(map[string][]byte)
str := "abc"
for i, v := range []byte(str) { // avoid memory allocation
  t.Logf("i, v: %+v, %+v\n", i, v) // 97, 98, 99
}
```

更安全的方式：

```go
import (
    "reflect"
    "unsafe"
)

func String(b []byte) (s string) {
    pbytes := (*reflect.SliceHeader)(unsafe.Pointer(&b))
    pstring := (*reflect.StringHeader)(unsafe.Pointer(&s))
    pstring.Data = pbytes.Data
    pstring.Len = pbytes.Len
    return
}

func Slice(s string) (b []byte) {
    pbytes := (*reflect.SliceHeader)(unsafe.Pointer(&b))
    pstring := (*reflect.StringHeader)(unsafe.Pointer(&s))
    pbytes.Data = pstring.Data
    pbytes.Len = pstring.Len
    pbytes.Cap = pstring.Len
    return
}
```

## 运算

- 位运算优先级高于四则运算

## 其他

- struct, array, slice, map的比较
  - struct内的所有字段都可以用`==`来比较，那么struct可以用`==`来比较；
  - array的每个元素都能用`==`比较时才可以比较；

- panic恢复，`recover()`必须在defer函数或者语句中调用，否则无效

```go
package main

func doRecover() {
  fmt.Println("recover: %+v", recover()) // will not recover, nil
}

func main() {
  defer func() {
    fmt.Println("recovered: ", recover()) // will recover
  }

  defer func() {
    doRecover() // will not recover
  }

  panic("failed")
  // recover() // put it here is useless
}
```

Mostly end here. 