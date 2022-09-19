---
layout: article
title: 【Golang】Golang Slice解析
sidebar:
  nav: BASICS
aside:
  toc: true
key: golang_slice
tags:
- 301-work-golang
- 301-work-basics
- 301-work-interview
category: [work, golang]
---

# Golang Slice解析

## slice实现

Slice是Golang提供的数据结构，slice的底层实现是这样的：

```go
// Definition of slice
type slice struct {
    array unsafe.Pointer // Pointer to underlying data array
    len int // slice length
    cap int // max array size
}
```

## slice更新操作

由于slice传递至函数的是*引用*，所以作为函数参数传入到函数体内，对slice进行改变是会影响最终结果的。例如下面的例子：

```go
func changeArr(arr []int) {
  arr[0] = 1
}

subArr = arr[:2]
arr := []int{0, 1, 2}
changeArr(subArr) // arr = [1, 1, 2]
```

根据以上可以看到，如果对函数的传入参数进行修改，那么会改变slice内部的值，切片也是在原片基础上进行的，并没有创建新的slice，所以对切片的修改会影响到原数组。

## slice的append操作

下面我们看到这个程序：

```go
func TestAppend(t *testing.T) {
	var s []int
	for i := 1; i <= 3; i++ {
		s = append(s, i)
	}
	reverse(s)
	t.Log(s)
}

func reverse(s []int) {
	s = append(s, 999)
	for i, j := 0, len(s)-1; i < j; i++ {
		j = len(s) - (i + 1)
		s[i], s[j] = s[j], s[i]
	}
}

/* result
> go test -run TestAppend -v
=== RUN   TestAppend
    general_test.go:252: [999 3 2]
--- PASS: TestAppend (0.00s)
PASS
ok      github.com/Chengru-Song/algo_practice/general   0.002s
*/
```

从这个结果可以看到，造成这个结果的原因是，传入函数的slice是值传递，`append`函数会创建一个新的slice，而这个slice的指针还是指向了相同的底层数组，因此会改变原来的数组值。
但是，原来传入的slice结构体的其他两个字段是值本身，所以len还是原来的3，并没有任何改变。

## slice的扩容机制

```go
func TestAppend2(t *testing.T) {
	var s []int
	for i := 1; i <= 3; i++ {
		s = append(s, i)
	}
  t.Logf("original cap: %+v", cap(s))
	reverse2(s)
	t.Log(s)
}

func reverse2(s []int) {
	s = append(s, 999, 1000, 1001)
	for i, j := 0, len(s)-1; i < j; i++ {
		j = len(s) - (i + 1)
		s[i], s[j] = s[j], s[i]
	}
  t.Logf("appended cap: %+v", cap(s))
}

/* result: 
=== RUN   TestAppend2
    general_test.go:269: original cap: 4
    general_test.go:280: appended cap: 8
    general_test.go:271: [1 2 3]
--- PASS: TestAppend2 (0.00s)
PASS
ok      github.com/Chengru-Song/algo_practice/general   0.002s
*/
```

上面这个例子为什么没有改变原来slice的值呢，这与slice的最大容量有关，我们可以看到上面数组的最大容量是4，所以当我们append的元素超过最大cap时候会resize重新创建数组，此时原来函数中的slice和函数中的slice已经指向的是不同的底层数组了，这也是为什么这次运行并没有改变原来的slice。

## Take away notes

1. Golang中slice结构体包含指向底层数组的指针，数组长度和最大长度，函数传递时，对slice进行值传递，也就是说对指针，长度和最大长度进行了复制，此时在函数中改变slice的内部值，会改变原slice的内部值。

2. 如果进行`append`操作，不涉及到扩容情况下，函数中对slice进行改变会影响原slice的值，但是不会改变原slice的len和cap，扩容的话则新slice的ptr指向新的底层数组，任何对其进行的改变将不会影响到原slice。

