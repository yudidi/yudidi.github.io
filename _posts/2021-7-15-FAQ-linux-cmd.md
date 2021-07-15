---
layout: post
title: linux常用的命令
---

# 1.查看占用端口的进程
## 方法1
netstat -tunlp | grep 1111
## 方法2
lsof –i: 端口号
* 用途

1. 需要杀死端口时,需要看下这个端口被哪个进程占用的。

# 2.查看进程占用了哪些端口
1. 查找进程
ps -ef | grep tomcat

2. 根据进程找端口
netstat -nltp | grep 11694