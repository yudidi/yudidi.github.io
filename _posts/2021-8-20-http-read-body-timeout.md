---
layout: post
title: Go的http请求成功,响应码200,但是读取body报超时错误
---

# 背景
调用第三方接口时发现的报错
```cmd
日志中错误:net/http: request canceled (Client.Timeout exceeded while reading body)
```

# demo
```go
fastFailHttpClient := &http.Client{Timeout: time.Second * 2}
resp, httpErr = ad.fastFailHttpClient.Do(req)
    if httpErr != nil || resp == nil || resp.StatusCode == 404 {
       // 建议重试
    }
    defer func() { _ = resp.Body.Close() }()
    break
}
if httpErr != nil || resp == nil {
    //
}
respCode = resp.StatusCode
if respCode == 200 {
    tmpBody, errRead := ioutil.ReadAll(resp.Body)
    if errRead != nil {
        logger.Error("read resp error", zap.String("err", errRead.Error())) //日志中错误:net/http: request canceled (Client.Timeout exceeded while reading body)
        return
    }
```

# 原因TODO

# 处理方案
增加Timeout

# 附录
1.[Go 网络处理中的几种错误-- 客户端读取超时：已建立好连接，已经开始返回数据，但是body 太大太慢](https://romatic.net/post/go_net_errors/)
