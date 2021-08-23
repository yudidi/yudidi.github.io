---
layout: post
title: 从⽤户进程创建socket,到⼀个⽹络包抵达⽹卡到被⽤户进程接收到的总体流程
---

# 背景
在分析粘包和半包问题时,对于Go net包下的Client的Read和Write方法的底层原理产生了一些疑问`,

```go
// client.go 通过套接字发送消息
conn.Write([]byte(words))
// server.go 通过套接字读取消息
n, err := conn.Read(buffer)
```

# 网络通信中的套接字是什么鬼

# 主要内容
// 附录1
从⽤户进程创建 socket，到⼀个⽹络包抵达⽹卡到被⽤户进程接收到，总体上的流程图如下：
![_config.yml]({{ site.baseurl }}/images/content/20210821-how-linux-socket.png)


# TCP客户/服务器程序所需的基本套接字函数
// 附录2
![_config.yml]({{ site.baseurl }}/images/content/20210823-tcp-c-s-model.png)

# 附录
1.本机: 理解了实现再谈网络性能.pdf-1.3 TCP下用户进程如何和内核协同？
2.本机: UNIX网络编程卷1：套接字联网API（第3版）-第4章 基本TCP套接字编程
