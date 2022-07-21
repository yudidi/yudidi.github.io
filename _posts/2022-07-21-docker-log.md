---
layout: post 
title: docker logs
categories: [docker]
tags: [docker]
---

# docker logs原理
docker logs 
命令会吧容器内的stdout、stderr 标准输出和标准错误都显示出来。具体原理见下文

# 使用
```shell
# 查看qp容器的最新日志
docker logs --tail 10 qp -f
```

#
1.[docker logs](https://blog.csdn.net/liuxiao723846/article/details/121864929)
2.[Docker笔记（十三）：容器日志采集实践](https://juejin.cn/post/6844904113528897549)