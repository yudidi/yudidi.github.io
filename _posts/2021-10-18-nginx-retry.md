---
layout: post
title: Nginx失败重试机制
---

# 背景
我负责的广告下发业务. upstream模块配置转发到4台机器，后来我停止了1台机器，但是忘记改upstream配置了。

这个错误到配置持续了2天多，直到今天周一才发现，但是业务却没有任何问题。

这引起了我的困惑。

和同事简单讨论,了解到nginx有重试机制。

猜想应该是这个原因才使得业务没有出现。

另外，我也考虑增加一些监控，使得能够及时发现错误的配置信息，主要思路是监控nginx的错误日志(error.logk)

# 错误配置时,错误日志如下

```go
2021/10/18 16:48:29 [error] 2828#0: *88397155 connect() failed (111: Connection refused) while connecting to upstream, client: 49.7.38.70, server: open.aplum.com, request: "GET /adds/weibo-notify?微博广告监测链接", host: "xxx.xxx.com"
```

# 重试的具体过程

# 附录
1.[Nginx失败重试机制](https://zhuanlan.zhihu.com/p/127959800)
