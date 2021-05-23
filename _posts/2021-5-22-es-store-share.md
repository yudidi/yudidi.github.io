---
layout: post
title: es存储原理-内部技术分享 
---
// TODO es term 和term index 选择term时 与hash表的不同. fst

分两部分

1 es 使用的搜索引擎库 ，lucene是什么？

2 es在lucene基础上，做了哪些事情？


Lucene 是什么？

倒排索引 inverted index，指的是一种索引方法，
建立的是分词（Term）和文档（Document）之间的映射关系

相关的名词
index（索引）：名词指的 一堆有相似结构的 文档数据，与mysql里的database 类似
动词指的是， insert的同时创建一个索引

文档 & 属性： 一个document是一条实例数据，类似mysql里的 行；
属性（field）类似于 表里的字段

Term 词条：最小的存储 和 查询单元，英文里的一个单词，中文里分词后的一个词

Term Index：可以快速地定位到Term Dictionary 的某个 key，加速 以及 节省内存空间
数据结构是变种的trie树   // TODO ydd 这里好像是fst数据结构，具体还要再看看

Term dictionary词典：词条Term 的集合，类似与哈希表
单词到 倒排列表之间的关系；key 是词， value是 倒排表，

Post list 倒排表 ：记录 term 到 document的之间的关系

posting 倒排索引项： 倒排表里的每一项数据，包括了以下信息

1 文档ID
2 词频，这个term在这篇 document中的 出现的次数
3 位置position， term在文档中的位置，用于位置相关的搜索，phrase query
4 偏移位置offset，记录term开始结束的位置，实现高亮显示

索引到词之间的关系：

索引（Index）→ 段（Segment）→ 文档（Document）→ 域（Field）→ 词（Term）

Lucene 一个 Index 会包含多个 Segment；
一个 Segment 又由多个文件共同组成，记录的一组关系 及 原始数据：

xx.tip：存储 Term Index
xx.tim：存储 Term Dictionary
xx.doc：存储 Postings 的 DocId 信息和 Term 的词频

行存储
xx.fnm：存储文档 Field 的元信息
xx.fdx：存储文档的索引，使用 SkipList 来实现
xx.fdt：存储具体的文档

列存储，排序，聚合的功能、等
xx.dvm：存储 DocValues 元信息
xx.dvd：存储具体 DocValues 数据

lucene创建索引流程
1 Tokenizer：分词阶段
2 预处理，大小写
3 索引阶段，此阶段会创建字典、创建倒排表

lucene 搜索流程
1 分词
2 预处理
3 搜索，term-> term index->  term dic -> post list-> posting ，获取相关文档
4 结果排序



ElasticSearch 封装了哪些东西？

基于Lucene的分布式搜索引擎。 分布式 + 搜索

集群(cluster)， 一组协同工作的服务器集合，用来提供比单一服务更稳定、更高效、更具扩展性的服务平台；

在外界来看，集群是一个独立的服务实体（透明的）

node的概念
两种节点，master节点、data节点

conf/elasticsearch.yml:
    node.master: true/false
    node.data: true/false
master节点：可参加选举，PacificA 算法
实现cap原理，明细：https://www.alibabacloud.com/blog/elasticsearch-distributed-consistency-principles-analysis-3---data_594360）

data节点：只用做数据存储
既不是master，也不是data：类似proxy-like作用的节点，聚合结果返回给请求

另，集群中的任意节点，都可以接受请求



Shard 分片
1 是一个最小的 Lucene 索引单元, 是 Lucene 的一个实例

2 es集群支持水平扩展能力的方案；分片被分配到集群内的各个节点里；

3 主分片（primary shard）、副本分片（replicas）

主分片：每个分片有 数据量的上限，分片的数目，直接决定着集群中能够保存的最大数据量，不可更改
副本分片：拷贝，主要是用来实现高可用、高并发的，例如：硬件故障时保护数据不丢失，提供读服务等。

比如3节点，1主2副本， 每个节点被分配到一个分片；（kafka里partition分区的副本 分配方式类似）

数据同样会被 balance ，每个分片数据量大致趋同



与lucene的关系
es的每个Index 有个多个shard，每个shard相当于一个 Lucene实例；
而每个Lucene有个多个 segment，每个 Segment 意味着一组映射关系：

es索引原理？路由

shard = hash(routing) % number_of_primary_shards（分片数量）
Routing是一个可变值，默认是文档的 _id，

意味着
1 一旦分片数量确定，不能变更，否则数据会错乱；只能重建，也就是，不是一致性哈希
2 拿到_id，每个节点都知道集群中任意一个文档所处的位置

写操作
1 根据公式计算，数据在哪个分片
2 先在主分片上面完成之后，将数据复制到其他节点的分片副本；
3 等其他所有节点返回后，返回写的结果

index、create、update 都是写操作

1 只有等到所有 副本返回（不一定写成功），才真正返回，也就是同步的方式，延迟取决于最慢的那个副本
2只要返回了，在主分片 还是 副本分片 ，都是可用的，

那么问题来了，会不会读到脏数据？比如3节点，主分片与其中1个副本成功，其中一个副本失败
当写失败时，主分片告知 master，master把这个有脏数据的副本（节点）摘除，也就是这个副本（节点）不能再读了

可能读到，但时间很短，或者说不是强一致性的

读，取文档
1 根据公式计算，数据在哪个分片
2 负载均衡获取一个副本（也可以是主分片），将请求转给这个副本
3 获取数据，返回， 也就是执行lucence相关操作

更新文档
1 根据公式计算，数据在哪个分片
2 请求转到主分片
3 修改 _source 字段中的 JSON ，并且尝试重新索引主分片的文档。
   如果文档已经被另一个进程修改，它会重试步骤 3，超过 retry_on_conflict 次后放弃
4 如果节点 3 成功地更新文档，它将新版本的文档并行转发到其他节点上的副本分片，重新建立索引。
   一旦所有副本分片都返回成功，节点 3 向协调节点也返回成功，协调节点向客户端返回成功


意味着update可能失败；



批量操作

mget操作、bulk操作，类似，区别并行操作，快一些



ElasticSearch 内部规则

1 协同多个shard（lucene）工作，而lucene本身高性能的全文检索工具包，倒排索引
2 倒排索引被写入磁盘后是不可改变的，如果你需要让一个新的文档可被搜索，需要重建整个索引。
   更倾向于读，对变更数据的效率进行了妥协。

   不变性意味着哪些好处，
   a 不用锁，b 缓存更容易维护，并且提高命中率，等等

3 避免单点故障，避免丢数据，通过translog 实现

动态更新索引

目的：为了保持不变性的前提下实现倒排索引的更新；
方式：通过追加新的补充索引来反映最近的修改，而不是直接重写整个倒排索引

注意：每一个倒排索引都会被轮流查询到,从最早的开始,查询完后再对结果进行合并
从索引中删除了大量文档，但这些文档只是做了删除标记，物理上并没有被删除

1 新的文档会被收集到内存索引缓存（In-memory buffer）中, 不可搜索

2 内存索引缓存生成一个新的 segment，先刷到文件系统缓存中；
   Lucene 这个时候是可以检索这个新的 segment 的；
   刷新的过程就是refresh；

3 这个时候丢数据咋整？也就是到以上步骤时还没有落盘，
ES写到内存索引缓存的同时 记录translog（linux 里write文件操作，延迟写）；
refresh发生，translog保持原样；
refresh 完成后, 内存索引缓存被清空,但是事务日志不会；

4 translog 是什么？类似于mysql里的write-ahead-log

fsync才是真正的I/O操作，也就是落盘
es默认每隔一定时间（5秒钟）会把translog刷新(fsync)到磁盘中，
意味着这时候宕机了，会丢失这部分数据；

恢复方式：从当前的 commit point （包含了此前生成的translog） 生成 lucene实例


commit point 是啥？
lucene，新增了commit point的概念，相当于一个标记（类似与版本） ，列出了所有已知的”段”的文件；

5 translog 什么时候主动flush？

两种方式，每隔一段时间 或 translog文件大小达到一定大小
比如，默认参数 30 分钟一次 flush,或者 translog 文件大小超过 500M 的时候

a 内存索引缓存被清空（refresh操作）
b 表示日志全部落到磁盘fsync，创建一个新的translog，旧的删掉
c 生成一个新的commit point

段合并
每次refresh都产生一个新段(segment)，频繁的refresh会导致段数量的暴增。
段数量过多，会消耗大量资源
a 消耗文件句柄
b 内存
c CPU时间 影响查询速度

所以Lucene干了什么事呢？通过 段的合并，来解决这个问题。

a 利用小的段合并到大的段，然后继续合并大的段，合并过程中会把已删除的文档从文件系统中清除，
b 这个过程是自动运行的，开发人员无感知。
c 这个阶段如果有索引刷新操作，会创建新的段并将段打开，在合并过程中不会中断索引。
d 段合并结束后，删除旧的段

什么时候促发合并？
段的合并，这个动作本身，会消耗掉大量系统资源CPU，尤其是磁盘I/O
会有相应的策略，衡量资源的投入产出（段的大小、可回收的文档数量越多），执行段的合并

# 总结
TODO

# 参考
https://xie.infoq.cn/article/55095e9626718380c4072f5fb
https://developer.aliyun.com/article/775303
https://www.infoq.cn/article/uotliglvj6tcue2vxc5x
https://www.infoq.cn/article/DMw6z0XcCSgCWS6TyTF1
https://developer.aliyun.com/article/108070
https://www.elastic.co/guide/cn/elasticsearch/guide/current/inside-a-shard.html
https://kkewwei.github.io/elasticsearch_learning/2019/10/17/ES%E6%AE%B5%E5%90%88%E5%B9%B6%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/