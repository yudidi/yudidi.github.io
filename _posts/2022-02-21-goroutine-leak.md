---
layout: post
title: 实战Go内存泄露
categories: [go]
tags: [go]
---

# 背景
内部项目(notify-srv-codeup)存在携程泄漏 4188ece1 jzw <xxxx@qq.com> on 2022/2/18 at 2:07 下午
从消息队列获取消息,每个消息产生一个携程,go监控的当前携程数量和消息发送数量相当。原因比较明细，但是该项目携程嵌套使用层次比较深。具体定位不到哪一行。

#

# 参考
5.[实战Go内存泄露](https://segmentfault.com/a/1190000019222661)
