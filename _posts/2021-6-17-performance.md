---
layout: post
title: 搜索服务2年的优化之路总结梳理
---

[我在公司内部进行的分享:2021 618大促优化记录.pdf](https://github.com/yudidi/yudidi.github.io/blob/master/docs/2021-06%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96%E8%AE%B0%E5%BD%95.pdf)


# 20210730-更新
今天看到一篇性能优化博客(附录1),其中有几点可以参考

1.辅助工具的使用
2.编写benchmark单独验证。//我之前性能优化只看pprof的结果和最终监控的响应时间，验证得还不够充分。
3.string转[]byte的黑科技
4.需要对redis原理有一定了解,才能知道redis hash需要取多次slot。// TODO 准备学习一些redis数据结构原理

# 附录
1.[一个95分位延迟要求5ms的场景，如何做性能优化](https://mp.weixin.qq.com/s/BUpsa22bQhK1pQKW8fUVOw)