---
layout: post
title: 今天定位了一台阿里云ECS机器的异常原因:OOM
---

# 群里收到报警信息

查看阿里云ECS的监控情况

![_config.yml]({{ site.baseurl }}/images/content/20210705-svc01-oom-kill.jpg)



最终怀疑是内存问题

pidstat -r 10 100 > pid_mem.lgo &


查看系统日志

![_config.yml]({{ site.baseurl }}/images/content/20210705-oom-varlog.png)


# 定位到原因是prometheus的metric无限增加,导致内存增加.
指标中包含了uid,这就导致每个用户都需要生成一个metric。

```
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
1.[图解阿里canal是怎么通过zookeeper实现HA机制的？](https://segmentfault.com/a/1190000023297973)
2.[理解canal的server和instance](https://blog.csdn.net/XDSXHDYY/article/details/97825508)
