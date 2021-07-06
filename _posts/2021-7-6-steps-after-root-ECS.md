---
layout: post
title: 从事故中总结一个:ECS服务器重启后的操作手册
---

昨天定位OOM的时候,有重启ECS服务器,重启之后，发现需要做一些基础准备工作,主要是几个agent程序。

```
1.手动重启go-cron的agent  // 定时任务调度框架
命令:
2.【已经添加开启启动项】自动部署需要运维手动重启/home/staragent/bin/staragentctl ，root权限  // 用于阿里云效流水线
3.【已经添加开启启动项】consul的agent需要手动重启 // consul服务注册发现的agent
命令:
```