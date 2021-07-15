---
layout: post
title: Go运行时的抢占式调度
---

# 本文的写作目的

首先,系统的理解Go调度相关的知识需要了解较多的底层知识，投入产出比很低，(明确自己的目的,知道不做什么很重要,人的精力真的是有限的，把每分每秒都压在刀刃上，才能更快达成目标不是么)

其次,对大部分软件而言,调度知识在工作中涉及很少,在软件设计，编码，测试阶段基本都不会涉及,只有性能优化的场景可能需要了解这块内容。

但是Go调度器的演变过程，是一个极好的性能优化的case，可以从中一瞥顶级程序员的改进和优化思路，令人上头。

我不希望在面试中背调度器八股文，而是希望能和读者或面试官探讨能从调度器的演变过程学到什么。

本文按照如下思路进行组织。

1.为什么Go需要一个调度系统,它需要做什么?

2.Go

Go语言调度器的实现机制是一个非常深入的话题，文末的附录3 推荐了几篇文章，特别值得探索学习。
我这里列举一下学习笔记。

Go语言调度器的领域分层图
1.什么情况下需要调度器
目前只有 Linux操作系统的进程调度,k8s,go runtime
2.

# 调度系统需求分析
我们在这一节中将从多个方面介绍调度系统设计时需要重点考虑的问题，其中包括调度系统的需求调研、调度原理以及架构设计。

|                    | 待分配的任务（Work） | 可执行任务的资源（Resource） |
|--------------------|--------------|--------------------|
| 操作系统的进程调度器(linux)  | Thread       | CPU 时间             |
| 容器编排系统(Kubernetes) | Pod          | Node(CPU、GPU 和内存等) |
| go-runtime         | Goroutine    | 在 CPU 上运行的线程       |


# 协作式调度和抢占式调度 
// 附录5

## go为什么需要自己实现调度?

# 协作式调度依靠被调度方主动弃权；抢占式调度则依靠调度器强制将被调度方被动中断。 
// 附录5
这两个概念其实描述了调度的两种截然不同的策略，这两种决策模式，在调度理论中其实已经研究得很透彻了。

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

3.[关于 Go1.14，你一定想知道的性能提升与新特性-1.2 goroutine 支持异步抢占](https://gocn.vip/topics/9611#1.2%20goroutine%E6%94%AF%E6%8C%81%E5%BC%82%E6%AD%A5%E6%8A%A2%E5%8D%A0)

4.[Golang 汇编入门知识总结-NOSPLIT-TODO](https://blog.csdn.net/Tencent_TEG/article/details/108413692)

5.[6.8 协作与抢占](https://golang.design/under-the-hood/zh-cn/part2runtime/ch06sched/preemption/#682-)