---
title: flink module
date: 
tags: point
categories: flink
---

## 目录结构

- flink-annotations: Flink自定义的一些注解，用于配置、控制编译等功能。
- flink-clients: Flink客户端，用于向Flink集群提交任务、查询状态等。其中org.apache.flink.client.cli.CliFrontend就是执行./flink run的入口。
- flink-connectors: Flink连接器，相当于Flink读写外部系统的客户端。这些连接器指定了外部存储如何作为Flink的source或sink。例如对于kafka来说，flink-connector-kafka-xx定义了FlinkKafkaConsumer和FlinkKafkaProducer类分别作为Flink的source和sink，实现了对kafka消费和生产的功能。从图二可以看出，flink 1.9目前支持的外部存储有Cassandra、ES、Kafka、Hive等一些开源外部存储。
- flink-container: Flink对docker和kubernetes的支持。
- flink-contrib: 社区开发者提供的一些新特性。
- flink-core: Flink核心的API、类型的定义，包括底层的算子、状态、时间的实现，是Flink最重要的部分。Flink内部的各种参数配置也都定义在这个模块的configuration中。（这部分代码还没怎么看过，就不细讲了）。
- flink-dist: Flink编译好之后的jar包会放在这个文件夹下，也就是网上下载的可执行的版本。其中也包括集群启动、终止的脚本，集群的配置文件等。
- flink-docs: 这个模块并不是Flink的文档，而是Flink文档生成的代码。其中org.apache.flink.docs.configuration.ConfigOptionsDocGenerator是配置文档的生成器，修改相关配置的key或者默认值，重新运行这个类就会更新doc文件夹下的html文件。同样org.apache.flink.docs.rest.RestAPIDocGenerator是Flink RestAPI文档的生成器。
- flink-fliesystems: Flink对各种文件系统的支持，包括HDFS、Azure、AWS S3、阿里云OSS等分布式文件系统。
- flink-formats: Flink对各种格式的数据输入输出的支持。包括Json、CSV、Avro等常用的格式。
- flink-java: Flink java的API，就是写flink应用时用到的map、window、keyBy、State等类或函数的实现。
- flink-jepsen: 对Flink分布式系统正确性的测试，主要验证Flink的容错机制。
- flink-libraries: Flink的高级API，包括CEP（复杂事件处理）、Gelly图处理库等。
- flink-mesos: Flink对mesos集群管理的支持。
- flink-metrics: Flink监控上报。支持上报到influxdb、prometheus等监控系统。具体的使用配置可以在flink-core模块的org.apache.flink.configuration.MetricOptions中找到。
- flink-python: Flink对python的支持，目前还比较弱。
- flink-queryable-state: Flink对可查询状态的支持，其中flink-queryable-state-runtime子模块实现了StateClientProxy和StateServer。这两部分都运行在TaskManager上，StateClientProxy负责接收外部请求，StateServe负责管理内部的queryable state。flink-queryable-state-client-java子模块实现了QueryableStateClient，作为外部系统访问queryable state的客户端。
- flink-runtime: flink运行时核心代码，在第二节细说。
- flink-runtime-web: Flink Web Dashboard的实现。默认启动standalone集群后，访问http://localhost:8081 出现的界面。
- flink-scala: Flink scala的API。
- flink-scala-shell: Flink提供的scala命令行交互接口。
- flink-state-backends: flink状态存储的方式，目前这个模块中只有RocksDBStateBackend，未来可能会支持更多种的状态存储，以适应不同的业务场景。MemoryStateBackend和FsStateBackend的实现并不在这个目录下，而是在flink-runtime目录下。
- flink-streaming-java: Flink Streaming的java API。
- flink-streaming-scala: Flink Streaming的scala API。
- flink-table: Flink Table API，在第三小节中细说。
- flink-yarn: Flink对yarn集群管理的支持。

---

- flink-runtime模块是Flink最核心的模块之一，实现了Flink的运行时框架，如JobManager、TaskManager、ResourceManager、Scheduler、Checkpoint Coordinator

- flink-table模块属于Flink的上层API，包括java和scala版本的table-api，以及SQL的解析和SQL的执行。
  
  > 随着Flink SQL越来越受重视，flink-table从flink-libraries中移了出来，成为了独立的一级目录。Flink 1.9中，阿里把blink-planner开源了出来，这样整个flink-table中就有了2个planner。从长期来看，流批的统一是一个趋势，因此blink-planner只使用了StreamTableEnvironment中相关的API，而没有使用BatchTableEnvironment，将批当做一个有限的流来处理，希望通过这种方式实现流和批的统一。由于blink-table-planner更好的支持流批统一，且性能更好，在未来的版本中，很有可能完全替代flink-table-planner的功能，而flink-table-planner可能将会被移除。
