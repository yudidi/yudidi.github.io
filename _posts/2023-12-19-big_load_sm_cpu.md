---
layout: post
title: CPU使用率低负载高
categories: [go]
tags: [性能优化]
---

# 使用场景

我发现我本地磁盘使用了300多g,想清理一下，最开始使用`du -h --threshold=1G -d 1 /path/to/directory来查看指定目录下超过1GB的文件和子目录的磁盘占用情况`
但是该命令导致机器的负载从5，变为25，电脑很卡，所以改为使用以下go代码进行扫描，结果跑几十秒后负载也上去了。

```go
package main

import (
	"fmt"
	"os"
	"path/filepath"
)

func main() {
	root := "/"
	err := filepath.Walk(root, func(path string, info os.FileInfo, err error) error {
		if err != nil {
			fmt.Printf("Error accessing path %q: %v\n", path, err)
			return nil
		}
		if info.Size() > 1<<30 { // 判断是否超过1GB
			fmt.Printf("%q: %d bytes\n", path, info.Size())
		}
		return nil
	})
	if err != nil {
		fmt.Printf("Error walking the path %q: %v\n", root, err)
	}
}

```

我的电脑配置:ubuntu 22,16核,32g，htop查看到负载很高时,cpu使用率不超过50%，内存也没啥变化，主要就是软件占用的内存，差不多10g，也没啥变化。

大概搜索了一下，负载和cpu使用率没有很大联系，负载表示的是平均活跃进程数，只能说明最近几分钟有活跃的进程，并且这些进程不怎么占用cpu，而是占用的io，或者等待io,或者一直在竞争cpu但是没有使用。

涉及的知识点:

1.处于r和d状态的进程会算入负载的的进程数。

进一步排查的方法：

1.iotop 查看io占用大的进程.

2.iostat 查看磁盘负载iostat

# 参考

1.[记一次CPU使用率低负载高的排查过程](https://juejin.cn/post/6844904173843005447)

tips：系统load高，不代表cpu资源不足。Load高只是代表需要运行的队列累计过多。但队列中的任务实际可能是耗cpu的，也可能是耗i/0及其他因素的

但是system的中断数（in)、上下文切换（cs）特别频繁，进程上下文切换次数较多的情况下，很容易导致CPU将大量的时间耗费在寄存器、内核栈、以及虚拟内存等资源的保存和恢复上，进而缩短了真正运行进程的时间造成load高。

* 排查单个进程的具体线程的上下文切换情况

通过vmstat只能查看总的cpu上下文切换，可通过pidstat命令查看线程层面的上下文切换信息 `pidstat -wt 1`
（下图拉的是9s的数据，总共36w次，平均每秒4w次）

cswch/s:

nvcswch/s: TODO 比如IO等待让出???

2.[【Linux负载系列-2】Linux CPU 使用率低 Load 负载高场景测试](https://www.ebpf.top/post/sys_linux_load_avg2/)

3.[load高但是cpu占用率低的排查](https://www.cnblogs.com/51core/p/13640104.html)

4.[top命令输出解释以及load average 详解及排查思路](https://blog.csdn.net/zhangchenglikecc/article/details/52103737)

5.[进程上下文频繁切换导致load average过高](https://www.cnblogs.com/lihuaichen/p/15186410.html)

* 1、LMbench 是带宽（读取缓存文件、内存拷贝、读写内存、管道等）和反应时间（上下文切换、网路、进程创建等）的评测工具；

* 2、micro-benchmark contextswitch 可以测试不同的CPU在最少多少ns可以进行一次上下文件切换，再转化为秒，我们可以确认该处理器每可以进行的上下文件切换数
  ，该工具的使用可以参看tsuna的blog。

cswch/s: 每秒任务主动(自愿的)切换上下文的次数，当某一任务处于阻塞等待时，将主动让出自己的CPU资源。

nvcswch/s: 每秒任务被动(不自愿的)切换上下文的次数，CPU分配给某一任务的时间片已经用完，因此将强迫该进程让出CPU的执行权。

6.[【Linux负载系列-1】Linux 系统的平均负载（Load Avarage）](https://www.ebpf.top/post/sys_linux_load_avg1/)

内核中计算平均负载的源码

```c
long calc_load_fold_active(struct rq *this_rq, long adjust)
{
	long nr_active, delta = 0;

	nr_active = this_rq->nr_running - adjust;
	nr_active += (long)this_rq->nr_uninterruptible;

	if (nr_active != this_rq->calc_load_active) {
		delta = nr_active - this_rq->calc_load_active;
		this_rq->calc_load_active = nr_active;
	}

	return delta;
}
```

```text
这段代码来自于 Linux 内核的 loadavg.c 文件，它负责计算系统的平均负载。具体来说，平均负载是衡量系统活动的指标之一，表示系统在一段时间内运行的进程数量和等待CPU资源的进程数量的平均值。

代码解析如下：

calc_load_fold_active 函数用于计算每个 CPU 的活跃进程数，并将其变化量（delta）返回。活跃进程数是指正在运行的进程（nr_running）和不可中断状态的进程（nr_uninterruptible）的和。

nr_active 通过从当前 CPU 的正在运行的进程数中减去一个调整值（adjust），然后加上不可中断状态的进程数来计算。这种不可中断状态一般表示进程正在等待某些类型的 I/O 操作完成。

如果新计算的活跃进程数与最后一次记录的值（保存在 this_rq->calc_load_active）不同，代码将计算两者之间的差值（delta），更新 calc_load_active，并返回这个差值。

在这段代码中，注释提到了分布式计算负载的方式。由于在多 CPU 系统中，遍历所有 CPU 来计算平均负载可能会非常耗时，所以 Linux 采取分布式和异步的方式来最小化计算开销。每个 CPU 计算自己的活动进程数的变化量，并将其贡献到全局平均负载的计算中。

此外，注释还提到了几个与 NO_HZ（即时钟节能模式）相关的复杂性，以及 nr_uninterruptible 的统计可能并不是精确的，因为为了避免增加额外的跨 CPU 缓存行访问和原子操作，它可能在不同 CPU 之间被统计。

总体来看，这段代码是内核计算平均负载的关键部分，它使用了一些性能优化的技巧来处理在具有大量 CPU 的系统上计算负载的问题。
```

```markdown
Q: (long)this_rq->nr_uninterruptible; 这个状态的进程一般是什么情况

A:
在 Linux 操作系统中，进程状态 `(long)this_rq->nr_uninterruptible` 表示进程处于不可中断睡眠状态（D状态），这通常是因为进程正在等待某种不可忽略的事件完成，通常是 I/O 操作。在这种状态下，进程不会响应异步信号。这个设计的目的是为了保护进程在关键操作期间不被打断，从而避免数据的不一致或者损坏。

不可中断睡眠状态的进程通常涉及到下面这些情况：

1. **磁盘 I/O**：进程正在等待磁盘读写操作完成，例如从硬盘读取数据或向硬盘写入数据。

2. **网络 I/O**：进程可能在等待网络操作完成，如等待网络数据包的到来。

3. **设备 I/O**：如果进程正在与其他类型的设备通信（比如 USB 设备或者其他外围设备），它可能需要等待设备的响应。

4. **等待硬件条件**：进程可能在等待一些特定的硬件条件，比如等待某个硬件中断。

在这个状态下，进程将被排除在调度器可运行进程队列之外，并且不会被分配CPU时间，直到它等待的事件被处理。一旦 I/O 或其他等待事件完成，进程状态会转变，使其能够继续执行。

在 `calc_load_fold_active` 函数内部，这个状态的进程数量被统计并包含在系统负载的计算中，因为这些进程虽然当前不占用 CPU 资源，但它们表示了系统中未完成的工作量。由于这些进程一旦 I/O 完成就可能立即变为可运行状态，它们对系统负载的影响是有意义的。
```