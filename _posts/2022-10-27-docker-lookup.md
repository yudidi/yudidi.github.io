---
layout: post 
title: DOCKER默认虚拟网卡IP地址段导致的网络访问异常问题(线上事故)
categories: [docker]
tags: [线上事故]
---


# 现象
php的前端服务，部署在k8s中,多个pod，调用部署在阿里云物理机器的http服务， 偶尔差不多20%的请求会很慢(耗时30s左右)。

调用链路:

php(k8s pod) -> 阿里云SLB(http负载均衡) -> 阿里云ECS(2台物理机器)http服务

1.注意2台物理机器里面装了docker,产生了虚拟网卡,且虚拟网卡的网段和k8s的某些机器的网段重了。
这个docker并没有启动任务服务,只是安装docker,且产生了虚拟网卡.

2.pod可以ping通物理机,但是无法telnet通物理机。

# 问题原因
简单说就是，www在k8s里面，请求阿里云物理机器的推荐服务，推荐服务的物理机装了docker产生虚拟网卡和k8s的pod的地址重了。
导致www的请求过来在 推荐服务的机器上，一直回环绕，直到底层的tcp链接超时了，才返回报错。


# 问题解决
1.修改docker虚拟网卡的ip。
2.非必要不安装docker.
3.尽量迁移k8s部署。


# 参考资料

1.[DOCKER默认虚拟网卡IP地址段导致的网络访问异常问题](https://blog.csdn.net/weixin_37569048/article/details/105580867)
2.[docker网段冲突导致主机网络异常处理](https://blog.51cto.com/u_14621529/5525320)