---
layout: post
title: 2021-8-30-go1.16.md
categories: [Go]
tags: [Go]
---



# 背景
```es
GET /xxxx/product/_search
{
  "_source":false,
  "from": 0,
  "query": {
    "bool": {
      "filter": [
        {
          "term": {
            "visible": 1
          }
        },
        {
          "term": {
            "is_es_show": 1
          }
        },
        {
          "bool": {
            "should": [
              {
                "terms": {
                  "status": [
                  ]
                }
              },
              {
                "term": {
                  "liveapp": 1
                }
              }
            ]
          }
        }
      ]
    },
    "size": 10,
    "sort": [
      {
        "liveapp": {
          "order": "desc"
        }
      }
    ]
  }
}
```

## 报错信息

```es
{
  "error": {
    "root_cause": [
      {
        "type": "parsing_exception",
        "reason": "[bool] malformed query, expected [END_OBJECT] but found [FIELD_NAME]",
        "line": 50,
        "col": 5
      }
    ],
    "type": "parsing_exception",
    "reason": "[bool] malformed query, expected [END_OBJECT] but found [FIELD_NAME]",
    "line": 50,
    "col": 5
  },
  "status": 400
}
```

# 知识点
// 附录1
1.filter工作于bool查询内。 工作原理是: 查询过程中，优先经过filter过滤，因为should是或关系，龙套偏房和魏行首的年龄符合了filter过滤条件，也就被放行了！
2.filter与bool平级,会报错。

# 附录
1.[Elasticsearch-布尔查询 -> 注意：filter工作于bool查询内。比如我们将刚才的查询条件改一下，把filter从bool中挪出来](https://www.cnblogs.com/Neeo/articles/10578625.html)