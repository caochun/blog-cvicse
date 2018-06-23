---
title: 高速计费实时数据处理框架
date: 2018-05-10 22:52:00
tags: 项目纪要
description: 交通事业部的流式业务数据处理框架。

---
## 数据处理模式说明

### 流式数据处理介绍

流式数据是指由数千个数据源持续生成的数据，通常也同时以数据记录的形式发送流式数据处理系统中进行处理。这一类数据在现实生活中分布广泛，例如用户使用您的移动或 Web 应用程序生成的日志文件、网购数据、游戏内玩家活动、社交网站信息、金融交易大厅或地理空间服务，以及来自数据中心内所连接设备或仪器的遥测数据。
对这一类流式数据的处理方式， 通常是按照记录或时间进行窗口划分按顺序进行递增式处理。

### 实时数据分析

在流式数据处理中一类典型的任务是进行实时的数据处理和分析，也就是在数据进入或者到达流式数据处理系统之后，在不经过等待或者极小的等待时间，不进行持久化的数据保存等操作的情况下进行数据的处理和分析的工作。这一类任务的主要类型包括实时的数据分析、机器学习，实时的可视化的信息的展示，根据不断检测到的数据判断系统的变化，进而迅速作出相关决策或者建议，以及一些实时的查询响应等任务。

### 对比

与传统的批处理任务相比，在传统模式下，数据产生后通常会保存在数据库或者某些文件系统中，之后程序在对这些数据进行处理，而在流式数据处理系统中，数据在到达系统后不会直接进行保存，而是直接进行分析和处理的操作，将结果在保存或者输出。

![批处理模式](/images/tolling/batchprocess.png)
批处理模式
![流处理模式](/images/tolling/streamprocess.png)
流处理模式

## 传统的高速公路数据处理模型

以高速公路收费站为代表，说明高速公路模型中对数据的收集、传输、处理的过程。

在汽车上下高速，进出收费站的过程中，车道会扫描每辆车辆的信息，包括车辆类型、车牌信息等等，然后添加时间、车站信息、车道信息等形成一条流水，车道收集每辆汽车的流水数据后，会在车站的数据库进行汇总，在这些数据上可以执行一些数据分析的任务。同时，车站会不定期将自己保存的数据上传到上级的中心数据库，比如省级中心数据库。中心数据库将所有收集到的数据进行汇总保存，再在上面执行相关的数据分析任务，包括车费的计算、校验，路费分摊等等。整个处理过程可以用下面的图表示：

![传统高速公路数据处理模型](/images/tolling/batchfreeway.png)
传统高速公路数据处理模型

这是传统的数据处理模型，整个过程主要是数据收集和后期的静态数据的分析。仔细观察可以发现，整个模型只能处理和解决没有短时时间要求的任务，数据汇总传输的较慢，数据处理的任务也很单一。不能解决实时的数据任务，比如中心数据库想要获得实时的全局的道路交通信息，实时的对每一辆出站的汽车进行路费的计算任务等。如果使用支持流式数据处理任务的系统，则可以解决这一类问题。

在车道得到每一条扫描到的数据之后，立即进入流式数据处理系统，进行计算处理，一边将结果进行保存，一边将数据马上发送到中心数据库，中心数据库也对收到的来自每一个车站的数据进行实时的数据分析和保存。这样在上级中心就可以实时的得到一个全局的信息，完成很多有实时行要求的任务。处理模型如下图所示：

![改进的高速公路数据处理模型](/images/tolling/streamfreeway.png)
改进的高速公路数据处理模型

## 高速计费模型

根据改进的数据处理模型，可以实现很多传统模式下无法处理的任务，比如传统的车辆在高速公路上行驶时的收费问题。

### 传统收费模式

车辆入站领卡---车辆出站交卡交费

特点：

- 卡片记录信息
- 人工收费、操作效率低
- 高速路口容易产生堵车

### ETC模式

车辆安装相关设备，在高速路口扫描该设备自动扣款

特点：

- 安装麻烦
- 会遮挡部分视线
- 申请周期长，可能需要安装费

### 利用流式数据处理框架的改进模型

在现在的高速公路车辆行驶过程中，有很多数据采集的过程，对于这些已有数据配合相关大数据工具可以设计一个新的收费方案，需要的数据和技术有：

- 车辆在高速公路的出入口都会扫描车牌，产生流水数据
- 产生的数据可以实时的上传保存到中心数据库中，实际可能不是实时，但理论上可以做到
- 流式数据处理框架配合大数据存储系统可以做到实时的数据处理和数据查询工作，理论上可以在很短的时间内处理一个出入数据，并计算车费
- 中间检查点的信息，可以帮助判断和计算车辆行驶的确切路径，得到可靠的计算结果

![系统设计图](/images/tolling/system.png)

## 系统架构

系统设计主要可以分为四个步骤：数据收集-入站记录数据库保存-出站记录匹配与处理-其他数据分析

系统组成主要包括四个部分：
数据收集和缓存、
流式数据处理、
结果可视化、
数据持久化存储。

### 数据收集和缓存

Kafka作为一个使用发布 - 订阅消息机制的分布式流式数据缓存平台，是一种典型的生产者消费者模型，数据源产生数据后不断将数据传送到Kafka里的一个topic队列中，对该topic进行订阅的消费者，也就是在我们系统中的流式数据处理模块，会不断地对这个topic获取数据。在这里Kafka主要起到数据缓存的作用。它的特点如下：

- 一个使用发布 - 订阅消息机制的分布式流式数据缓存平台
- 可以接收不同格式的数据，包括文本、图片、视频等
- 具有低延时、高可靠性
- 实现数据入载平衡

### 流式数据处理

系统的核心部分。Apache Flink是一个面向分布式数据流处理和批量数据处理的开源计算平台，它能够基于同一个Flink运行时，提供支持流处理和批处理两种类型应用的功能。可以提供高吞吐率、低延时性的保证。同时能够很好地解决数据乱序问题、提供丰富的窗口划分机制、SQLAPI的集成等。类似的处理平台还有storm、spark streaming等。但很多实验都表明，在数据处理的吞吐率和延时等问题上，flink都有更好的保证。

- 一个开源的处理流式数据和批数据的系统
- 具有高吞吐率和低延时性
- 可以处理数据的乱序等问题
- 结果较准确

### 实时化的结果展示

ElasticSearch是一个高度可扩展的开源搜索引擎并使用REST API,能够达到实时搜索，稳定，可靠，快速的目标，在使用flink对数据处理完成之后，对需要可视化的数据发送到es中响应的index，之后再借助kibana，可以迅速在网页中获得直观的展示。Kibana十一个专门用于分析和可视化的开源平台，继承了各种不同的图表、表格、地图，能轻易地和es进行交互，展示高级数据和可视化。

### 数据持久化存储

数据的持久化存储，使用hbase 和 hive平台。其中hbase 用来处理实时的数据请求和查询操作，在实验中主要处理出入站数据记录的查询匹配，路径路费的查询匹配等操作。而对保存在hive中的数据，支持进行后续的统计分析，机器学习等任务。

## 已有代码和使用Flink编程的说明

### Fink流式任务

一个Flink任务可以划分为三个部分：source、task、sink。
一个基本流程是：

- 首先创建一个StreamExecutionEnvironment对象，设置相关参数
- 设置source
- 完成对数据要进行的处理，也就是task部分
- 设置对结果的输出，即sink

创建StreamExecutionEnvironment对象（Scala）

``` scala
val env: StreamExecutionEnvironment = StreamExecutionEnvironment.getExecutionEnvironment
```

创建StreamExecutionEnvironment对象（Java）

``` java 
final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
```

### source

source确定任务处理的数据来自于哪里，代码里用到的是 Kafka队列，另外还有从数据文件模拟了一个流数据产生的过程。官方API包含的source有
Apache Kafka,Amazon Kinesis Streams,RabbitMQ,Apache NiFi,Twitter Streaming API等等。

source类继承自SourceFunction，主要实现的方法是run(sourceContext: SourceContext[T])，
一个StreamExecutionEnvironment对象在启动后会对添加的source调用run方法，传入sourceContext参数，该source通过不断向sourceContext写入数据，实现一个数据流的过程。

添加source（Scala）：

``` scala
val data: DataStream[T] = env.addSource(new SourceFunction())
```

添加source（Java）：

``` java
DataStream<T> data = env.addSource(new SourceFunction());
```

简单说明代码里用到的 source：

#### WasteRecordSource.scala

WasteRecord是流水记录类，定义的是一条出入站的流水记录。在`datatypes\WasteRecord.scala`中定义。

这里主要做的是打开保存好的流水数据文件，逐条读取每个记录，然后按照时间顺序，或者乱序（数据进入系统的顺序和它们产生时的顺序不一致）的方式将数据发送到stream中进行处理。其中可以控制速度，设置每条记录间的延时等。

另外有关watermark等的相关概念，可以阅读官方文档里eventtime部分[Generating Timestamps / Watermarks](https://ci.apache.org/projects/flink/flink-docs-release-1.4/dev/event_timestamps_watermarks.html)

#### WateRecordFromKafkaSource.scala

这是自己实现的一个连接Kafka队列的source，事实上官方API已经提供了对 Kafka的支持。

主要做的就是使用了Kafkaconsumer的java API，不断从一个已知队列获取数据，放入sourcecontext。

### sinks

sinks确定处理完的数据被放到何处，官方API的支持有Apache Kafka、Apache Cassandra、Amazon Kinesis Streams、Elasticsearch、Hadoop FileSystem、RabbitMQ、Apache NiFi、Twitter Streaming API等等。

sink在这个版本中继承自RichSinkFunction等，主要实现的方法有：open和invoke。其中open在开始时被调用一次，invoke对每次处理完的数据逐个调用。

添加sink(scala):

``` scala
filteredData.addSink(new SearchInSink())
```

#### ElasticsearchUpsertSink.scala

将处理好的数据放入es，即可视化展示

#### PutHbaseSink.scala && PutOutHBaseSink.scala

这里做的是将处理过的记录放入hbase表格中，调用了hbase的java API，根据一些需求不同，有一些小的不同。

#### SearchInSink.scala

对出站数据匹配入站数据，即查找hbase中的相关记录。
对于流水记录的出入站匹配，路费计算部分我都是放在这里完成。可以在计算完，得到结果后再将结果输出到某个地方，意思就是，sink不仅仅只用来决定数据结果的输出，也可以进行未完成的计算。

❓❓❓

### application

介绍完成的几个小实验。这里做的都是对数据的简单filter。几乎没有用到窗口机制、table API，以及更高级的CEP、ML等等。
复杂功能的例子可以参阅Flink GitHub的例子[flink-examples-streaming](https://github.com/apache/flink/tree/master/flink-examples/flink-examples-streaming).

#### SearchTable.scala

这里输入的数据是入站和出站的混合，做了简单的filter后，将入站数据和出战数据分开，分别对入站数据和出站数据设置sink，将入站的数据保存在hbase中，然后出站数据会查询与自己对应的入站数据。

filter (Java)

```java
DataStream<WasteRecord> startRides= rides.filter(new FilterFunction<WasteRecord>() {
            @Override
            public boolean filter(WasteRecord x) {
                return x.isStart();
            }
        });
```

路径匹配，路费计算没有放在这里，而是放在sink中。

#### TotalWaste.scala

这里做的是简单的模拟数据流动的过程，从单个文件读数据，即source是WasteRecordSource，不断放入es的过程。

#### TotalWasteFromKafka.scala

这里做的也是简单的模拟数据流动的过程，但是是从Kafka对了读数据，即source是WasteRecordFromKafkaSource，不断放入es的过程。

#### TotalaWasteSlidingWindow.scala

