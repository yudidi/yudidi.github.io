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


我的电脑配置:ubuntu 22,16核,32g，htop查看到负载很高时,cpu使用率不超过50%，内存也没啥变化，就是软件占用的内存，差不多10g。

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

通过vmstat只能查看总的cpu上下文切换，可通过pidstat命令查看线程层面的上下文切换信息 `pidstat -wt 1`（下图拉的是9s的数据，总共36w次，平均每秒4w次）

cswch/s:

nvcswch/s: TODO 比如IO等待让出???

2.[【Linux负载系列-2】Linux CPU 使用率低 Load 负载高场景测试](https://www.ebpf.top/post/sys_linux_load_avg2/)

3.[load高但是cpu占用率低的排查](https://www.cnblogs.com/51core/p/13640104.html)

4.[top命令输出解释以及load average 详解及排查思路](https://blog.csdn.net/zhangchenglikecc/article/details/52103737)

5.[进程上下文频繁切换导致load average过高](https://www.cnblogs.com/lihuaichen/p/15186410.html)

* 1、LMbench 是带宽（读取缓存文件、内存拷贝、读写内存、管道等）和反应时间（上下文切换、网路、进程创建等）的评测工具；

* 2、micro-benchmark contextswitch 可以测试不同的CPU在最少多少ns可以进行一次上下文件切换，再转化为秒，我们可以确认该处理器每可以进行的上下文件切换数 ，该工具的使用可以参看tsuna的blog。

cswch/s: 每秒任务主动(自愿的)切换上下文的次数，当某一任务处于阻塞等待时，将主动让出自己的CPU资源。

nvcswch/s: 每秒任务被动(不自愿的)切换上下文的次数，CPU分配给某一任务的时间片已经用完，因此将强迫该进程让出CPU的执行权。