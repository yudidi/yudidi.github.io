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

## 方法3
// 附录1
ss -ltnp |grep 端口号

```bash
# -l 表示只显示监听套接字
# -t 表示只显示 TCP 套接字
# -n 表示显示数字地址和端口(而不是名字)
# -p 表示显示进程信息
```

netstat 和 ss 的输出也是类似的，都展示了套接字的状态、接收队列、发送队列、本地地址、远端地址、进程 PID 和进程名称等。

# 2.查看进程占用了哪些端口
1. 查找进程
ps -ef | grep tomcat

2. 根据进程找端口
netstat -nltp | grep 11694


# 查看谁正在使用某个文件(用户+进程)
// 附录2,3,4
```json
[xxx@pre pre-price-srv]$ lsof pre-price-srv
COMMAND     PID  USER  FD   TYPE DEVICE SIZE/OFF    NODE NAME
pre-price 13569 xxx txt    REG 253,17 83395167 2382348 pre-price-srv
pre-price 13570 xxx txt    REG 253,17 83395167 2382348 pre-price-srv
```

使用fuser来查文件或目录被谁占用

# 参考
1.[34 | 关于 Linux 网络，你必须知道这些（下）](https://time.geekbang.org/column/article/81057)
2.[linux查文件被哪些程序占用—fuser和lsof的使用](https://blog.csdn.net/happyteafriends/article/details/12993691)
3.[lsof命令使用场景](https://blog.csdn.net/dengbixuan/article/details/106817389)
4.[fuser=使用文件或文件结构识别进程](https://wangchujiang.com/linux-command/c/fuser.html)