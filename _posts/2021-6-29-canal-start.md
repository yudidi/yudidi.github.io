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
最近在搭建一套Canal同步环境,又遇到了一些问题,这里参考附录6{Canal v1.1.4版本避坑指南}做一些记录.

## Q: 现象: Canal运行一段时间就会出现,增量数据不同步的问题.
报错: `Caused by: com.alibaba.otter.canal.parse.exception.CanalParseException: column size is not match for table:数据库名称.表名称,新表结构的字段数量 vs 缓存表结构的字段数量;`

原因: 表结构缓存

## Q: intances消费日志出现: No route info for this topic, [MQ实例]%[topic名字]
```
Caused by: java.lang.RuntimeException: org.apache.rocketmq.client.exception.MQClientException: No route info for this topic, xxxxx%yyyyyy
```
```
Caused by: java.lang.RuntimeException: org.apache.rocketmq.client.exception.MQClientException: No route info for this topic, [MQ实例]%[topic名字]
```

A:原因 未配置阿里云RocketMQ的访问密钥 ak/sk
```
# aliyun ak/sk , support rds/mq
#canal.aliyun.accessKey =
#canal.aliyun.secretKey =
```

当时用这个错误在canal的项目里面找到了这个issue[v1.1.5 连接阿里云RocketMQ失败 #3064](https://github.com/alibaba/canal/issues/3064)

但是当时不理解ak/sk简写的具体含义,也就错过了这个解决方案。

后来,通过不断的对比配置,缩小配置错误的范围,最终找到了原因,配置之后就OK了.

我也给这个issue提交了comment,说明了[ak/sk的意思](https://github.com/alibaba/canal/issues/3064#issuecomment-900976087),希望能帮助后来者.

## Q:控制canal同步的起始位点: 更新zk中消息的位点
一、控制同步起始位置

* 需求背景: 我希望监控我启动

预发布环境RocketMQ下，canal在A1时间点启动，然后A2时间点停止；

经过一段时间后，B1时间点再次启动canal服务:此时canal会先同步A2到B1时间段之间的数据，

但是希望直接从B1(2020-10-14 14:00:00)时间点开始同步数据。

* 重置位点方案
1. 停止instance

2. 删除zk中的位点信息  rmr /otter/canal/destinations/dev02db

3. 启动instance

# 参考
1.[阿里开源MySQL中间件Canal快速入门](https://blog.csdn.net/qqxx6661/article/details/106039464)

2.[开源实战 | Canal生产环境常见问题总结与分析](https://cloud.tencent.com/developer/article/1645881)

TODO
3.[一文详解 Canal Instance 设计理念与定制开发思路-Instance 是 Canal 数据同步的核心，在一个 Canal 实例中只有启动 Instace，才能实现数据的同步](https://bbs.huaweicloud.com/blogs/218136)

4.[Mysql （master/slave）主从复制原理及配置](https://www.jianshu.com/p/510876797e31)

5.[阿里Canal配置内容详解](https://my.oschina.net/u/585635/blog/4436514)

6.[Canal v1.1.4版本避坑指南](https://mp.weixin.qq.com/s/U8tTO4OS9idx08exCD0rug)