---
layout: post
title: Nginx的URL编解码处理机制

---

# 背景
重构时，从旧服务对nginx拷贝日志中的url出来，验证新服务能获取同样的结果。

在Gin框架的bind方法发现,ua参数不能正确的bind.

from=douyinxingtu&os=1&ua=Mozilla%2F5.0+%28iPhone%3B+CPU+iPhone+OS+14_6+like+Mac+OS+X%29+AppleWebKit%2F605.1.15+%28KHTML%2C+like+Gecko%29+Mobile%2F15E148&ip=112.111.55.167&ts=1626883143543&model=iPad11,3&demand_id=6966874113549795332&order_id=6966874119816085598&item_id=6976127045222911238&callback_param=EJiw267wvfQCGKf2g74ZIPD89-vIATAMOAFCIjIwMTkxMTI3MTQxMTEzMDEwMDI2MDc3MjE1MTUwNTczNTBIAQ==&callback_url=https://ad.toutiao.com/track/activate/?callback=EJiw267wvfQCGKf2g74ZIPD89-vIATAMOAFCIjIwMTkxMTI3MTQxMTEzMDEwMDI2MDc3MjE1MTUwNTczNTBIAQ==&os=0&muid=db94e6a60a9c6661e3e03a7d301c25b6"


from=douyinxingtu
os=1
ua=Mozilla%2F5.0+%28iPhone%3B+CPU+iPhone+OS+14_6+like+Mac+OS+X%29+AppleWebKit%2F605.1.15+%28KHTML%2C+like+Gecko%29+Mobile%2F15E148
ip=112.111.55.167
ts=1626883143543
model=iPad11,3
demand_id=6966874113549795332
order_id=6966874119816085598
item_id=6976127045222911238
callback_param=EJiw267wvfQCGKf2g74ZIPD89-vIATAMOAFCIjIwMTkxMTI3MTQxMTEzMDEwMDI2MDc3MjE1MTUwNTczNTBIAQ==
callback_url=https://ad.toutiao.com/track/activate/?callback=EJiw267wvfQCGKf2g74ZIPD89-vIATAMOAFCIjIwMTkxMTI3MTQxMTEzMDEwMDI2MDc3MjE1MTUwNTczNTBIAQ==
os=0
muid=db94e6a60a9c6661e3e03a7d301c25b6"


# 附录
1.[Nginx的URL编解码处理机制](https://www.yangwenchao.com/post/tech/2020/2020-01-09-nginxurlprocess/)
