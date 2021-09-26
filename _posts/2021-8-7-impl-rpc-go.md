---
layout: post
title: 自己动手写RPC(一)-需求篇
categories: [自己动手写RPC系列]
tags: [自己动手写RPC系列]
---

# 背景
看了一篇自己动手写RPC框架的博客,觉得通过造轮子来学习的方式很好。


# 预备知识
1.进程间通信的方式
2.为什么

# Q:What is Serialization?
## Q:什么是序列化
// 附录1
A: 为了把内存中的对象进行网络传输和持久化存储,需要把内存对象转换为字节数组的过程.
The string (an in-memory object) abc would be serialized as 0x61 0x62 0x63 (ASCII) or 0x00 0x61 0x00 0x62 0x00 0x63 (UTF16) 

Q:为什么要转换为字节数组

A:因为网络传输和持久化存储的方式都是IO,IO的基本单位都是字节数组,所以必须转换为字节数组。

## Q:什么是反序列化
A: 把网络字节数组或持久存储中的字节数组,还原为内存中对象的过程.

## Q:为什么需要序列化
// 附录2
A:

1. 序列化是为网络传输和持久化存储,而这两者的方式都是IO,IO支持的数据格式只有字节数组.
所以当要传输或存储内存对象时,都需要把内存对象转换字节数组。

2. 对象转成字节数组的时候就制定一种规则

# 立项
假设,现在你有1个进程

# What:什么是RPC


# Why:为什么需要RPC

## 你想想，如果没有 RPC 框架，那你要怎么调用另外一台服务器上的接口呢?

# 主要内容
Go的几种RPC实现

RPC通信过程基本流程: 序列化-编码-解码-反序列化

RPC通信过程代码流程

# 附录
1.[What is Serialization?](https://stackoverflow.com/posts/633410/timeline)
2.[为什么要序列化？](https://zhuanlan.zhihu.com/p/40462507)
3.[Go net/rpc 包的深度解读和学习](https://cloud.tencent.com/developer/article/1662215)
4.[这里有一篇,Java系列的RPC框架,是基于Netty搭建网络模块的: 一起写RPC框架（三）RPC网络模块的搭建一 网络传输模型](https://blog.csdn.net/linuu/article/details/52213911)
