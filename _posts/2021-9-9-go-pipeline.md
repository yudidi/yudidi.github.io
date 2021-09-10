---
layout: post
title: GO编程模式PIPELINE (Golang并发模型：流水线模型)
categories: [Go]
tags: [Go]
---

# 背景
推荐算法的同事交接了部分算法的项目给我。

该部分业务的流程类似如下逻辑:

本来的实现是：

发现可以采用这个模式进行处理，提升性能。

# Q:什么业务可以 fan out and fan in(merge)

# FAN-IN和FAN-OUT模式
// 附录2
FAN-OUT模式：多个goroutine从同一个通道读取数据，直到该通道关闭。OUT是一种张开的模式，所以又被称为扇出，可以用来分发任务。

FAN-IN模式：1个goroutine从多个通道读取数据，直到这些通道关闭。IN是一种收敛的模式，所以又被称为扇入，用来收集处理的结果。

## FAN模式一定能提升性能吗？


# 流式计算与批量计算
// 附录3

# 附录
1.[GO编程模式：PIPELINE](https://coolshell.cn/articles/21228.html#Channel_%E7%AE%A1%E7%90%86)
2.[Golang并发模型：流水线模型](https://www.cnblogs.com/-wenli/p/12349831.html)
3.[流式计算与批量计算](https://zhuanlan.zhihu.com/p/81849938)