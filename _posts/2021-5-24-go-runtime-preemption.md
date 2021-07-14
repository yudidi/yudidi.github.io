---
layout: post
title: Go的抢占式调度
---

```
func main() {
	//runtime.GOMAXPROCS(1)
	var ops uint64 = 0
	for i := 0; i < 50; i++ {
		go func() {
			for {
				atomic.AddUint64(&ops, 1)
				//fmt.Print()
			}
		}()
	}
	time.Sleep(time.Second)
	opsFinal := atomic.LoadUint64(&ops)
	fmt.Println("ops:", opsFinal)
}
```

# 参考
1.[为何这段go程序不能让出控制权？](https://www.zhihu.com/question/308020301)
1.1[为何这段go程序不能让出控制权？ - 月明星稀的回答 - 知乎](https://www.zhihu.com/question/308020301/answer/573432965)

2.[Go: Goroutine and Preemption](https://medium.com/a-journey-with-go/go-goroutine-and-preemption-d6bc2aa2f4b7)

3.[Golang 汇编入门知识总结-NOSPLIT-TODO](https://blog.csdn.net/Tencent_TEG/article/details/108413692)