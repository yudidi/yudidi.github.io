---
layout: post
title: 面试题 && 同事互问题
---

# 背景

1.技术群有新人提问: 有大佬能用简单易懂的方式向面试官解释倒排索引吗?

2.有个新同事新接触es,问我倒排索引怎么理解.

3.之前有同事面试,也被问过该问题, 所以写篇博客记录一下.

# 我的回答
一本字典，我给你一个字，你需要告诉我这个字在字典的哪一页。
我理解这就是正排索引。

一本字典，我给你一个词，你需要告诉我这个词在字典的哪些页出现了。
你需要设计一个怎么样的数据结构来处理这个问题。
这就是倒排索引。

# 附录
1.[官网-倒排索引](https://www.elastic.co/guide/cn/elasticsearch/guide/current/inverted-index.html)
2.[漫画趣解：透析Elasticsearch原理](https://mp.weixin.qq.com/s/DryL2Mr2UK0Yiv_jLfdatg)