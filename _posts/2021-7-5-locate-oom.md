---
layout: post
title: prometheus使用姿势错误导致监控指标大量增长最终导致OOM的复盘总结
---

# 钉钉群里收到报警信息
一时找不到原因，让运维同事重启了服务器，先解决问题再定位原因。

# 查看阿里云ECS的监控情况

![_config.yml]({{ site.baseurl }}/images/content/20210705-svc01-oom-kill.jpg)

先怀疑cpu,磁盘问题,同事们回想一下最近的修改,主要是消息中心和接入阿里云的日志服务。

浏览了最近提交的一些服务的代码，没有看出异常。

重启服务器后，使用vmstat、pidstat 等性能工具，观察系统和进程的 CPU 使用率。 // 附录1

```
# 所有进程的 CPU 统计信息
pidstat -u 1 10

# 显示 IO 统计信息
pidstat -d

# 显示内存统计信息
pidstat -r 10 100 > pid_mem.lgo &

发现有个服务的内存占用不断的缓慢增加
```

# 查看系统日志，确实有oom-kill的记录
![_config.yml]({{ site.baseurl }}/images/content/20210705-oom-varlog.png)

# 查看服务的supervisor日志
发现同一时间服务收到SIGKILL信号的重启
![_config.yml]({{ site.baseurl }}/images/content/20210705-supervisor-sigkill.png)


# alloc的pprof文件定位到问题函数
重启服务器和服务后,获取内存的pprof文件

![_config.yml]({{ site.baseurl }}/images/content/20210705alloc-pprof.png)

# 查看源码定位到原因是prometheus的metric无限增加,导致内存增加.
指标中包含了uid,这就导致每个用户都需要生成一个metric。

```go
// getOrCreateMetricWithLabelValues retrieves the metric by hash and label value
// or creates it and returns the new one.
//
// This function holds the mutex.
func (m *metricMap) getOrCreateMetricWithLabelValues(
	hash uint64, lvs []string, curry []curriedLabelValue,
) Metric {
	m.mtx.RLock()
	metric, ok := m.getMetricWithHashAndLabelValues(hash, lvs, curry)
	m.mtx.RUnlock()
	if ok {
		return metric
	}

	m.mtx.Lock()
	defer m.mtx.Unlock()
	metric, ok = m.getMetricWithHashAndLabelValues(hash, lvs, curry)
	if !ok {
		inlinedLVs := inlineLabelValues(lvs, curry)
		metric = m.newMetric(inlinedLVs...)
		m.metrics[hash] = append(m.metrics[hash], metricWithLabelValues{values: inlinedLVs, metric: metric}) // 这个不断增加
	}
	return metric
}
```

# 参考
1.[每天学习一个命令：pidstat 查看进程消耗资源](https://einverne.github.io/post/2019/05/pidstat-usage.html)