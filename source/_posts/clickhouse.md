---
title: clickhouse points
date: 2021-12-29 19:11:30
tags: point
categories: clickhouse
---

## 存储架构

> Clickhouse 存储中的最小单位是 DataPart，写入链路为了提升吞吐，放弃了部分写入实时可见性，即数据攒批写入，一次批量写入的数据会落盘成一个 DataPart.
> 
> 它不像 Druid 那样一条一条实时摄入。但 ClickHouse 把数据延迟攒批写入的工作交给来客户端实现，比如达到 10 条记录或每过 5s 间隔写入，换句话说就是可以在用户侧平衡吞吐量和时延，如果在业务高峰期流量不是太大，可以结合实际场景将参数调小，以达到极致的实时效果。

## 查询架构

### 计算能力方面

Clickhouse 采用向量化函数和 aggregator 算子极大地提升了聚合计算性能，配合完备的 SQL 能力使得数据分析变得更加简单、灵活。

### 数据扫描方面

ClickHouse 是完全列式的存储计算引擎，而且是以有序存储为核心，在查询扫描数据的过程中，首先会根据存储的有序性、列存块统计信息、分区键等信息推断出需要扫描的列存块，然后进行并行的数据扫描，像表达式计算、聚合算子都是在正规的计算引擎中处理。从计算引擎到数据扫描，数据流转都是以列存块为单位，高度向量化的。

### 高并发服务方面

Clickhouse 的并发能力其实是与并行计算量和机器资源决定的。如果查询需要扫描的数据量和计算复杂度很大，并发度就会降低，但是如果保证单个 query 的 latency 足够低（增加内存和 cpu 资源），部分场景下用户可以通过设置合适的系统参数来提升并发能力，比如 max_threads 等。其他分析型系统（例如 Elasticsearch）的并发能力为什么很好，从 Cache 设计层面来看，ES 的 Cache 包括 Query Cache, Request Cache，Data Cache，Index Cache，从查询结果到索引扫描结果层层的 Cache 加速，因为 Elasticsearch 认为它的场景下存在热点数据，可能被反复查询。反观 ClickHouse，只有一个面向 IO 的 UnCompressedBlockCache 和系统的 PageCache，为了实现更优秀的并发，我们很容易想到在 Clickhouse 外面加一层 Cache，比如 redis，但是分析场景下的数据和查询都是多变的，查询结果等 Cache 都不容易命中，而且在广投业务中实时查询的数据是基于 T 之后不断更新的数据，如果外挂缓存将降低数据查询的时效性。

## 技巧

### 唯一键约束

```sql
CREATE TABLE IF NOT EXISTS qilu.t_01(
	C1 String,
	C2 String,
	C3 String,
	C4 Date,
	PRIMARY KEY (C1) # 要设置主键
) engine=ReplacingMergeTree() # 引擎要用ReplacingMergeTree
 ORDER BY C1; # 要设置排序

optimize table t_01 FINAL; # 要强制合并分区
```

