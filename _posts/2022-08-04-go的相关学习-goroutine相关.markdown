---
title: go的相关学习-goroutine相关
author: not_you
date: 2022-08-04 00:00:00 +0800
categories: ["日常学习","go"]
tags: ["go"]
---

###  sync包

[官方链接 ](https://pkg.go.dev/sync)

#### WaitGroup

在需要多个任务同步的情况下，synct提供了WaitGroup。官方文档里的说明就已经足够清晰了，本质上就是信号量机制。值得一提的是，如果waitgroup结构体内部记录的数变为负值，将会触发panic。官方的使用代码示例如下

```golang
import (
	"sync"
)

type httpPkg struct{}

func (httpPkg) Get(url string) {}

var http httpPkg

func main() {
	var wg sync.WaitGroup
	var urls = []string{
		"http://www.golang.org/",
		"http://www.google.com/",
		"http://www.example.com/",
	}
	for _, url := range urls {
		// Increment the WaitGroup counter.
		wg.Add(1)
		// Launch a goroutine to fetch the URL.
		go func(url string) {
			// Decrement the counter when the goroutine completes.
			defer wg.Done()
			// Fetch the URL.
			http.Get(url)
		}(url)
	}
	// Wait for all HTTP fetches to complete.
	wg.Wait()
}

```

其中WaitGroup的结构体如下,noCopy表示不允许复制，只允许指针传递。容易理解，可以复制就无法同步了，甚至会造成需要同步用于同步的信号量的笑话，只有共用同一区域的同一变量，才能真正实现信号量机制。又看到，官方的注释中提到了，在32位架构下会有所区别。具体的处理操作要看WaitGroup定义的内部函数state(),涉及了指针转换。

```
type WaitGroup struct {
	noCopy noCopy

	// 64-bit value: high 32 bits are counter, low 32 bits are waiter count.
	// 64-bit atomic operations require 64-bit alignment, but 32-bit
	// compilers only guarantee that 64-bit fields are 32-bit aligned.
	// For this reason on 32 bit architectures we need to check in state()
	// if state1 is aligned or not, and dynamically "swap" the field order if
	// needed.
	state1 uint64
	state2 uint32
}
```

如图，state函数返回两个state的指针，并且在32位架构下需要进行一次指针转换

```
func (wg *WaitGroup) state() (statep *uint64, semap *uint32) {
	if unsafe.Alignof(wg.state1) == 8 || uintptr(unsafe.Pointer(&wg.state1))%8 == 0 {
		// state1 is 64-bit aligned: nothing to do.
		return &wg.state1, &wg.state2
	} else {
		// state1 is 32-bit aligned but not 64-bit aligned: this means that
		// (&state1)+4 is 64-bit aligned.
		state := (*[3]uint32)(unsafe.Pointer(&wg.state1))
		return (*uint64)(unsafe.Pointer(&state[1])), &state[0]
	}
}
```

后续的函数我就暂时不贴了，还要干活，逻辑其实很简单，具体有意思的可能就算是看一看信号量的实现吧。

[WaitGroup的链接](https://cs.opensource.google/go/go/+/refs/tags/go1.19:src/sync/waitgroup.go;l=62;bpv=1;bpt=0)

**留下一个问题之后有空解决：**115行的代码没理解，虽然不影响整体，之后再看

#### 参考

[有点不安全却又一亮的unsafe.Pointer](https://segmentfault.com/a/1190000017389782)