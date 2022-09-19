---
layout: article
title: 【Golang】Goroutine截获输出和错误
sidebar:
  nav: BASICS
aside:
  toc: true
key: golang_goroutine_return
tags:
- 301-work-golang
- 301-work-basics
- 301-work-interview
category: [work, golang]
---

# Goroutine获取输出
通过`chan`获取到输出。
参考这个程序代码:
<https://github.com/Chengru-Song/algo_practice/blob/master/conc/conc.go>

```go
func ConcPattern() {
	ch1 := make(chan int)
	ch2 := make(chan int)

	// var wg sync.WaitGroup
	// wg.Add(1)
	// wg.Wait()

	go func(ch chan int) {
		time.Sleep(time.Second * 1)
		ch <- 1
	}(ch1)

	go func(ch chan int) {
		// defer wg.Done()
		time.Sleep(time.Second * 2)
		ch <- 2
	}(ch2)

	for i := 0; i < 2; i++ {
		select {
		case msg1 := <-ch1:
			fmt.Printf("message from channel 1: %+v\n", msg1)
		case msg2 := <-ch2:
			fmt.Printf("message from channel 2: %+v\n", msg2)
		}
	}
}
```

# GoRoutine获取运行时Error

## 方式一
通过`chan error`来获取运行时的error，通过select或者获取chan的值进行错误处理，参考这里：
<https://github.com/Chengru-Song/algo_practice/blob/565eb8f08d7bb6edf89682e09d37db9217ffac62/conc/conc.go#L47>

```go
// First way to get error from goroutine,
// specify chan error, if error occurs, pass error to channel,
// handle the error
func generateError(input int) error { // a function that returns a normal error
	if input == 1 {
		return ErrFirst
	}
	return ErrSecond
}

func RunWithError() error {
	errc := make(chan error)
	exit := make(chan bool)
	go func() {
		err := generateError(1)
		if err != nil {
			errc <- err
			exit <- true
			return
		}
	}()

	for {
		select {
		case err := <-errc:
			if err == ErrFirst {
				return ErrFirst
			} else {
				return ErrSecond
			}
		case quit := <-exit:
			if quit {
				return ErrFirst
			}
		}
	}
}
```

## 方式二
使用封装好的`errgroup`包，这里面提供了一些批处理错误的方法，可以用来做goroutine的错误处理
<https://github.com/Chengru-Song/algo_practice/blob/565eb8f08d7bb6edf89682e09d37db9217ffac62/conc/conc.go#L85>

```go
func RunWithErrorGroup(ctx context.Context) error {
	errs, _ := errgroup.WithContext(ctx)

	// run all blocking request in parallel
	for i := 0; i < 4; i++ {
		errs.Go(func() error {
			time.Sleep(time.Duration(rand.Intn(100)) * time.Millisecond)
			return errors.New("request error")
		})
	}

	return errs.Wait()
}
```

以上就是两种能够从goroutine中获取到错误的方法。