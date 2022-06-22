---
layout: post
title: pprof排查思路梳理-TODO
categories: [pprof]
tags: [pprof]
---

# 背景
最近团队项目增多,频繁遇到内存泄漏,携程泄漏等问题。我们对pprof使用思路不是很清晰。这里梳理一下

# 什么场景应该用什么pprof?
附录1

1.内存泄漏

go tool pprof http://localhost:6060/debug/pprof/heap


2.携程泄漏

go tool pprof http://localhost:6060/debug/pprof/goroutine


3.gc问题(一般问题不大)

所以接下来使用 pprof 排查时，我们在乎的不是什么地方在占用大量内存，而是什么地方在不停地申请内存，这两者是有区别的。

```go
# 为了获取程序运行过程中 GC 日志，我们在启动前赋予一个环境变量，同时为了避免其他日志的干扰，使用 grep 筛选出 GC 日志查看：
GODEBUG=gctrace=1 ./go-pprof-practice | grep gc


go tool pprof http://localhost:6060/debug/pprof/allocs

```

## heap的采样原理
// 附录2
heap: 包含每个 goroutine 分配大小，分配堆栈等。每分配 runtime.MemProfileRate(默认为512K) 个字节进行一次数据采样。

# 实践 Tips
附录2

# 打印当前系统内存使用情况
```go
func showMemoryUsage() {
    Runtime runtime = Runtime.getRuntime();
    long free = runtime.freeMemory();
    long total = runtime.totalMemory();
    long used = total - free;

    long totalMB = total / (1024 * 1024);
    long usedMB = used / (1024 * 1024);
    LOG.debug("Memory usage: " + usedMB + " of " + totalMB + "M");
}
```

# 如何阅读pprof生成的图形
附录3,4


# FAQ:分配堆内存比较多的地方并不一定产生内存泄漏,比较2个时间点分配内存的差值,容易就是内存泄漏

现在你就可以比较这两个时间点的堆的差异了: go tool pprof --base base.heap current.heap
操作和正常的go tool pprof操作一样， 比如使用top查看使用堆内存最多的几处地方的内存增删情况


但是, 分配堆内存比较多的地方并不一定产生内存泄漏，只能说明这个地方"曾经/正在"分配的堆内存比较大,或者分配的堆内存比较频繁俄安，这些分配的内存可能在之后就回收掉了。

像Java的一些profiler工具一样， pprof也可以比较两个时间点的分配的内存的差值，通过比较差值，就容易看到哪些地方产生的内存"残留"的比较多，没有被内存释放，极有可能是内存泄漏的点。

[使用多年的go pprof检查内存泄漏的方法居然是错的---对比2个heap的对象变化](https://colobu.com/2019/08/20/use-pprof-to-compare-go-memory-usage/)

# 参考
1.[golang pprof 实战](https://blog.wolfogre.com/posts/go-ppof-practice/)
2.[go pprof 性能分析-pprof 数据采样](https://wudaijun.com/2018/04/go-pprof/)
3.[Go高性能系列教程：读懂pprof生成的报告](https://zhuanlan.zhihu.com/p/376191268)
4.[Go常用包(三十):性能调试利器使用(中)](https://liuqh.icu/2021/11/17/go/package/30-pprof-2)


