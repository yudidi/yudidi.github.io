---
layout: post
title: 干货 | Elasticsearch 向量搜索的工程化实战
categories: [es性能优化]
tags: [es性能优化]
---

# 背景
```json
{
    "settings": {
        "number_of_shards": 3,
        "number_of_replicas": 2,
        "index": {
            "routing": {
                "allocation": {
                    "require": {
                        "node_group": "hot" // 1）
                    }
                }
            },
            "store": {
                "preload": [ // 2)
                    "knowledge",
                    "category",
                    "available",
                    "confidence",
                    "del",
                    "kid"
                ]
            },
            "search": {
                "slowlog": {
                    "threshold": {
                        "query": {
                            "warn": "1s" // 3)
                        },
                        "fetch": {
                            "warn": "1s" // 3)
                        }
                    }
                }
            },
            "translog": {
                "flush_threshold_size": "512mb", // 4)
                "sync_interval": "5m", // 4)
                "durability": "async" // 4)
            },
            "sort": {
                "field": [ // 5)
                    "kid",
                    "confidence"
                ],
                "order": [ // 5)
                    "asc",
                    "desc"
                ]
            }
        }
    }
}
```

说明：
1.由于向量数据较大，所以倾向于将整个索引都放置在硬件性能更好的节点
2.为了支持高性能过滤，将常用的字段预先加载在内存中 // TODO
3.对慢查询开启日志方便后续性能问题的调查
4.知识库的重建是离线的，会在更新时进行大量写入，所以对 translog 的提交间隔拉长，加快写入速度 // TODO
5.在实际使用中kid是自增id，同时可能会对知识的置信度做排序等，所以会使用 sort field 存储这两个字段。 // TODO

# 从文章可以学到
1.提高过滤速度。将常用字段预先加入内存,空间换时间。

2.提高写入索引的速度。 增加translog刷盘间隔。

# 参考
1.[干货 | Elasticsearch 向量搜索的工程化实战](https://mp.weixin.qq.com/s/DtT5NhLOInIPgqbETzNOdg)