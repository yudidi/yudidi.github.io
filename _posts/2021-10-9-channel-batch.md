---
layout: post
title: 如何用 Golang 的 channel 实现消息的批量处理
---

# 背景
我负责迁移的广告下发服务,提供广告下发的监测链接接口.
流量波动特别,qps多时可以上千。旧服务是每次接口调用都写1次数据库,导致出现了线上事故,所以我这边进行优化。

优化思路:

将下发消息进行聚合后,批量写入数据库,这样可以极大的减少数据库操作.

# 附录
1.[如何用 Golang 的 channel 实现消息的批量处理](https://learnku.com/articles/34503)
2.[源码](https://github.com/Ksloveyuan/channelx/blob/master/aggregator.go)