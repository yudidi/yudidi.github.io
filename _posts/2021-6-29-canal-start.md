---
layout: post
title: 阿里开源MySQL中间件Canal快速入门
---

# Canal原理

# Canal设计理念 // 附录3
而 Canal 的设计初衷就是为了解决上述问题，故我们可以边思考这个场景，来反推一下 Canal Instance 的设计理念。

## Canal架构-server和instance的关系
https://zhuanlan.zhihu.com/p/345736518

* server 代表一个 canal 运行实例，对应于一个 jvm
* instance 对应于一个数据队列 （1个 canal server 对应 1..n 个 instance ) // 1个instance对应1个slave //附录3
* instance 下的子模块

# 正经的master/slave和伪装的slave

# Canal配置文件:canal.properties和instance.properties // 附录5

1. canal.properties存放instance.properties的共享参数

比如可以将instance.properties的公用参数，抽取放置到这里，这样每个instance启动的时候就可以共享.

2. instance.properties配置定义优先级高于canal.properties


# Canal生产环境问题

# 参考
1.[阿里开源MySQL中间件Canal快速入门](https://blog.csdn.net/qqxx6661/article/details/106039464)

2.[开源实战 | Canal生产环境常见问题总结与分析](https://cloud.tencent.com/developer/article/1645881)

TODO
3.[一文详解 Canal Instance 设计理念与定制开发思路-Instance 是 Canal 数据同步的核心，在一个 Canal 实例中只有启动 Instace，才能实现数据的同步](https://bbs.huaweicloud.com/blogs/218136)

4.[Mysql （master/slave）主从复制原理及配置](https://www.jianshu.com/p/510876797e31)

5.[阿里Canal配置内容详解](https://my.oschina.net/u/585635/blog/4436514)