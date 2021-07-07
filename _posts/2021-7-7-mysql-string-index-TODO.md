---
layout: post
title: 字符串前缀索引
---

# 现象
```sql
CREATE TABLE `xxx` (
  `id` int(10) NOT NULL AUTO_INCREMENT,
  `token` varchar(1024) NOT NULL COMMENT 'xxx',
  `resp_body` text NOT NULL COMMENT 'xxx',
  `resp_code` int(11) NOT NULL DEFAULT '0' COMMENT 'xxxx',
  `create_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  PRIMARY KEY (`id`),
  KEY `token` (`token`), // 长字符串索引
  KEY `resp_code` (`resp_code`),
  KEY `create_time` (`create_time`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='xxx';

```

执行上面的sql报错

`RDS MySQL提示“Error 1709: Index column size too large. The maximum column size is 767 bytes.`

# 处理

```sql
CREATE TABLE `xxx` (
  `id` int(10) NOT NULL AUTO_INCREMENT,
  `token` varchar(1024) NOT NULL COMMENT 'xxx',
  `resp_body` text NOT NULL COMMENT 'xxx',
  `resp_code` int(11) NOT NULL DEFAULT '0' COMMENT 'xxxx',
  `create_time` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  PRIMARY KEY (`id`),
  KEY `token` (`token`(10)), -- 字符串前缀索引
  KEY `resp_code` (`resp_code`),
  KEY `create_time` (`create_time`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='xxx';
```

# TODO 原理

为什么需要字符串前缀索引

# 参考
1.[RDS MySQL提示“Error 1709: Index column size too large. The maximum column size is 767 bytes.](https://help.aliyun.com/document_detail/155946.html)