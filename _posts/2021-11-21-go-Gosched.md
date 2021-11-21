---
layout: post
title: runtime.Gosched()函数
categories: [Go]
tags: [Go]
---

# 背景
我在线上使用这个库的代码,在看该库的实现的时候,发现了不常见函数的使用场景,
提了一个[issue](https://github.com/Ksloveyuan/channelx/issues/2),想知道为啥这么用。

```go
func (agt *Aggregator) TryEnqueue(item interface{}) bool {
	select {
	case agt.eventQueue <- item:
		return true
	default:
		if agt.option.Logger != nil {
			agt.option.Logger.Warnf("Aggregator: Event queue is full and try reschedule")
		}

		runtime.Gosched() // 这个函数的作用是让当前goroutine让出CPU，好让其它的goroutine获得执行的机会。同时，当前的goroutine也会在未来的某个时间点继续运行。
                //  过一会儿再次调度到这个携程，继续往下执行。要么入队成功，要么返回入队失败。
		select {
		case agt.eventQueue <- item:
			return true // 入队成功
		default:
			if agt.option.Logger != nil {
				agt.option.Logger.Warnf("Aggregator: Event queue is still full and %+v is skipped.", item)
			}
			return false // 入队失败
		}
	}
}
```

# `runtime.Gosched()`使用场景
这个函数的作用是让当前goroutine让出CPU，好让其它的goroutine获得执行的机会。同时，当前的goroutine也会在未来的某个时间点继续运行。

除了让出CPU，还可以通过自旋来等待一会。自旋的方式可以避免携程上下文的切换。

那么这样就会引出一个问题:什么时候该自旋,什么时候该让出当前携程? TODO ?

# 自旋锁的应用场景
// 附录2
自旋是自旋锁的行为，它通过忙等待，让线程在某段时间内一直保持执行，从而避免线程上下文的调度开销。自旋锁对于线程只会阻塞很短时间的场景是非常合适的

# 参考
1.[go runtime.Gosched()函数解析](https://blog.csdn.net/m0_37556444/article/details/100150622)
2.[自旋锁的应用场景](https://zhuanlan.zhihu.com/p/343563412)