---
layout: post
title: 【转载】i/o timeout,希望你不要踩到这个net/http包的坑
---

这篇博客从常见的`i/o timeout`现象入手,
经过抓包确认了是自连接创建3s后,http客户端主动断开连接(怀疑超时原因),
然后进一步跟踪源码,同时按照调用链路贴出源码,追踪出了超时原因和超时错误产生的位置.

整个过程环环相扣,读完印象深刻,好像自己分析定位出来的原因一样. 

从这篇文章可以学会如下技能:

1.抓包

2.golang net/http设置超时分为两种

2.1 自连接创建时开始计算的超时,如果这个连接被复用,那么每次请求时间都会累计到超时时间里。

2.2 每次请求的超时


3. net/http包复用长连接的缘由

4. net/http包复用长连接的实现(源码分析)

4.1 一个域名,一个长连接,每个连接对应2个携程(1个读,1个写)
所以,如果有N个域名,会有1+2*N个携程,1个建立连接的携程,N对读写携程 // 附录2,携程泄漏

4.2 超时的触发过程

4.3 `i/o timeout`在源码中产生的位置

5. 学习博客中如何贴源代码
按照调用链路贴出函数和关键代码行即可,力求让读者不用看源码,只用看博客的跟踪链路即可明白源码的链路.

# 参考
1.[i/o timeout ， 希望你不要踩到这个net/http包的坑](https://mp.weixin.qq.com/s/nifRx6FI2MclXiJqBIWFDw)
2.[携程泄漏分析](https://mp.weixin.qq.com/s?__biz=MzAwMDAxNjU4Mg==&mid=2247483794&idx=1&sn=0b617bf9acffc17afa5b3278d3d32eef&scene=21#wechat_redirect)
3.[给大家丢脸了，用了三年golang，我还是没答对这道内存泄漏题](https://mp.weixin.qq.com/s?__biz=MzAwMDAxNjU4Mg==&mid=2247483794&idx=1&sn=0b617bf9acffc17afa5b3278d3d32eef&scene=21#wechat_redirect)
这篇文章后面有很多面试题链接
```
连nil切片和空切片一不一样都不清楚？那BAT面试官只好让你回去等通知了。
昨天那个在for循环里append元素的同事，今天还在么？
golang面试官：for select时，如果通道已经关闭会怎么样？如果只有一个case呢？
golang面试官：for select时，如果通道已经关闭会怎么样？如果只有一个case呢？
golang面试题：对已经关闭的的chan进行读写，会怎么样？为什么？
golang面试题：对未初始化的的chan进行读写，会怎么样？为什么？
golang 面试题：reflect（反射包）如何获取字段 tag？为什么 json 包不能导出私有变量的 tag？
golang面试题：json包变量不加tag会怎么样？
golang面试题：怎么避免内存逃逸？
golang面试题：简单聊聊内存逃逸？
golang面试题：字符串转成byte数组，会发生内存拷贝吗？
golang面试题：翻转含有中文、数字、英文字母的字符串
golang面试题：拷贝大切片一定比小切片代价大吗？
golang面试题：能说说uintptr和unsafe.Pointer的区别吗？
```