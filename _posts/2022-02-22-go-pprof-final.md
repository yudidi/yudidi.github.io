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

# 参考
1.[golang pprof 实战](https://blog.wolfogre.com/posts/go-ppof-practice/)
2.[go pprof 性能分析-pprof 数据采样](https://wudaijun.com/2018/04/go-pprof/)
3.[Go高性能系列教程：读懂pprof生成的报告](https://zhuanlan.zhihu.com/p/376191268)
4.[Go常用包(三十):性能调试利器使用(中)](https://liuqh.icu/2021/11/17/go/package/30-pprof-2)