---
layout: post 
title: select中既有聚合字段，又有非聚合字段 
categories: [mysql]
tags: [mysql]
---

# 背景

```sql
SELECT
	t1.page_role,
	t2.page_id,
	t2.config,
	t2.style 
FROM
	t_page t1
	LEFT JOIN t_page_new_content t2 ON t1.id = t2.page_id 
WHERE
	t1.can_recommend = 1 
	AND t2.page_id IN (10758,10757,10761)
	AND t2.style IN ( 'img_tag_text', 'billboard', 'head_img_list' ) 
	AND ( ( start_time <= NOW( ) AND end_time > NOW( ) ) OR permanent = 1 ) 
	GROUP BY t2.page_id
	LIMIT 3
```

```sql

当t2.page_id 是主键或者唯一键时，这个语句就是合法的，不管sql_mod是不是 ONLY_FULL_GROUP_BY
```

# 阿里云配置

![_config.yml]({{ site.baseurl }}/images/content/20220521-关闭_only_full_group_by_阿里云rds.png)

![_config.yml]({{ site.baseurl }}/images/content/20220521-mysql8_only_full_group_by_valid.png)

# 参考

1.[关于MySQL中only_full_group_by模式的一点个人理解](https://www.jianshu.com/p/7f532985ff39)
2.[The query is valid if name is a primary key of t or is a unique NOT NULL column](https://dev.mysql.com/doc/refman/8.0/en/group-by-handling.html)