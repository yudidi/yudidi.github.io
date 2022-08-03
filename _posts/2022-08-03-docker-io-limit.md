---
layout: post 
title: 某一台容器的 IO 很高，就会导致同主机上的其他容器服务延时增加
categories: [docker]
tags: [limit]
---

# 背景
最近公司把服务部署都迁移到k8s上了,我对docker和k8s进行了一定的复习。

突然想到很久之前记录的关于 docker无法隔离io 的经历。 特此记录到博客中，以免未来忘了这事。


# 结论
原来docker不能隔离io，是因为所有容器都共享了bufferdio的原因。

我理解是如果一个高io容器占据了全部的 Bufferdio，那么肯定会导致其他容器无法使用Bufferdio。

这个问题是我在成都的公司遇到的，就是我们的容器无法隔离io，只要有一台容器频繁的访问磁盘，就会导致其他容器变慢，现在看来确实是这样。


# 美团通过修改内核,可以给每个容器单独配置bufferdio

![_config.yml]({{ site.baseurl }}/images/content/20211112-slowsql-COMMIT-IO.png)


# 美团扩展k8s增加了更多的限制,同时还修改了core dump的流程

1. 除了常见的 CPU、内存外，还有 `IO 的限制`、ulimit 限制、PID 限制等等。所以我们扩展了 K8s 来完成这些工作。

2. 对 core dump 的流程进行了修改，让 dump 文件写到容器自身的文件系统中，并且使用容器自己的 Cgroup IO 吞吐限制。

# 附录
1.[美团容器平台架构及容器技术实践](https://www.infoq.cn/article/hic8fc0TjQlzk8sPyBt8)