---
layout: post
title: 常见性能优化策略的总结
categories: [性能优化]
tags: [性能优化]
---

# 背景
```go
本文要感谢我职级评定过程中的一位评委，他建议把之前所做的各种性能优化的案例和方案加以提炼、总结，以文档的形式沉淀下来，并在内部进行分享。力求达到如下效果：
1. 形成可实践、可借鉴、可参考的各种性能优化的方案以及选型考虑点，同时配合具体的真实案例，其他人遇到相似问题时，不用从零开始。
2. 有助于开阔视野，除了性能优化之外，也能提供通用的常见思路以及方案选型的考虑点，帮助大家培养在方案选型时的意识、思维以及做各种权衡的能力。
文章在内部分享后，引起强烈分享，得到了不少同事和朋友的认可和好评，觉得对日常的工作有很好的指导作用。考虑到这些经验可能对业界同行也有帮助，所以在美团点评技术团队博客公开。
```

# 参考
1.[常见性能优化策略的总结](https://tech.meituan.com/2016/12/02/performance-tunning.html)