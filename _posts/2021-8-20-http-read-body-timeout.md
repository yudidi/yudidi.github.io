---
layout: post
title: Go的http请求成功,响应码200,但是读取body报超时
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

# 原因
// 附录1

1.客户端读取超时：已建立好连接，已经开始返回数据，但是body 太大太慢：

2.[源码注释](https://github.com/golang/go/blob/2bc8d90fa21e9547aeb0f0ae775107dc8e05dc0a/src/net/http/client.go#L104)

/usr/local/go/src/net/http/client.go:56
```go
type Client struct {
	// Timeout specifies a time limit for requests made by this
	// Client. The timeout includes connection time, any
	// redirects, and reading the response body. The timer remains
	// running after Get, Head, Post, or Do return and will
	// interrupt reading of the Response.Body.
	//
	// A Timeout of zero means no timeout.
	//
	// The Client cancels requests to the underlying Transport
	// as if the Request's Context ended.
	//
	// For compatibility, the Client will also use the deprecated
	// CancelRequest method on Transport if found. New
	// RoundTripper implementations should use the Request's Context
	// for cancellation instead of implementing CancelRequest.
	Timeout time.Duration
}
```

# 处理方案
增加Timeout

# 附录
1.[Go 网络处理中的几种错误-- 客户端读取超时：已建立好连接，已经开始返回数据，但是body 太大太慢](https://romatic.net/post/go_net_errors/)
