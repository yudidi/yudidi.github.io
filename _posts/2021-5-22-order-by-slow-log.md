---
layout: post
title: mysql排序时用到联合索引的后缀导致慢查询
---

![_config.yml]({{ site.baseurl }}/images/2021-05-22-slow-log.png)

```
SELECT ap.id, ap.product_id, ap.sku_id, ap.spu_id, ap.activity_id
	, ap.discount
FROM xxx ap
	JOIN yyy a ON a.id = ap.activity_id
WHERE a.activity_type = 'vip_discount'
	AND (ap.product_id IN (4099429, 4258467, 4531197, 4667974, 4677304, 4856925, 5140893, 6570089)
		OR ap.spu_id IN (4099429, 4258467, 4531197, 4667974, 4677304, 4856925, 5140893, 6570089))
	AND 1621664616 >= a.begin_time
	AND 1621664616 < a.end_time
ORDER BY ap.activity_id DESC // 修改为ORDER BY a.id DESC,速度即可变快
```

xxx的索引为
```
Create Table: CREATE TABLE `xxx` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '自动编号',
  `activity_id` int(10) unsigned NOT NULL,
  `product_id` int(10) unsigned NOT NULL,
  `order` int(10) unsigned NOT NULL COMMENT '在列表中的顺序',
PRIMARY KEY (`id`),
KEY `aid_pid` (`activity_id`, `product_id`),
KEY `product_id_activity_id` (`product_id`, `activity_id`),
KEY `idx_create_time` (`create_time`),
KEY `idx_updated_at` (`updated_at`),
KEY `idx_sku_id` (`sku_id`),
KEY `idx_spu_id` (`spu_id`),
KEY `idx_activityid_order` (`activity_id`, `order`)
);
```