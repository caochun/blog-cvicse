---
title: 上证所交易数据数据处理
date: 2018-06-20 22:52:35
tags: 项目纪要
---


### 项目逻辑和代码说明

#### 主要实现

根据已有静态历史数据文件（主要是对sh_20140312.zip这类文件中的Tick.csv做的处理），模拟每条记录产生后发送到kafka队列，然后使用flink流处理系统做实时处理，将结果放入hive数据仓库的过程。

##### 代码说明

##### Demo.SJ2Kafka

这个类读取data/Tick.csv文件，创建一个KafkaProducer类将其中的数据按照顺序放入到Kafka队列中，其中Kafka broker 设置的是host13:9092， topic 是sj.

##### com.dataartisans.flink_demo.examples.SJKafka2Hive

执行这个类会创建一个flink任务，该任务的source是Kafka，sink是hive,对应的类分别是`com.dataartisans.flink_demo.sources。SJFromKafkaSource`和`com.dataartisans.flink_demo.sinks.HiveSink`.

主要做的事情就是从之前放入数据的Kafka队列拉取数据，做一个简单的map之后放入hive数据仓库。

![架构](/images/sse/architecture-sse.png)

### 写入hive

个人理解是，Hive是对hdfs上的结构化数据文件做了一层关系型数据库的封装。当在hive中创建一个表时，指定这个表的location为hdfs上的一个directory，那个这个directory下的所有数据文件就是这个表中的数据，这些数据应该是结构化的类似csv格式的数据，并且格式与创建表时的schema对应。

那么不断对该directory增加数据文件，写入数据，就相当于传统关系型数据库的insert操作。

当在hive中对表执行一些SQL操作时，hive会把SQL语句转换成MapReduce任务，读取该表格对应的directory下的所有数据文件，得到结果。

使用flink系统将结果存入hive的过程，主要做的就是append/insert，所以可以使用flink的outputformat将结果直接以文件的形式写入到hdfs对应的目录下即可。注意这里写入的目录与hive中相应表格的location对应。

``` sql
CREATE EXTERNAL TABLE IF NOT EXISTS testflink3 ( wasteId string,time timestamp,locationx string, LOCATIONY STRING, stationId STRING,stationName STRING, vlp STRING)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
LOCATION '/user/root/testflink1';
```

``` scala
val format = new TextOutputFormat[String](new Path("/user/root/testflink1"))
format.writeRecord(record)
```

上面分别是创建hive中表格的命令和flink中输出目录，两者保持一致。另外，写入数据记录时，格式也要和表格的schema一致，包括各个属性，分隔符等。

#### Hive中内部表与外部表的区别： 

Hive 创建内部表时，会将数据移动到数据仓库指向的路径；若创建外部表，仅记录数据所在的路径， 
不对数据的位置做任何改变。在删除表的时候，内部表的元数据和数据会被一起删除， 
而外部表只删除元数据，不删除数据。这样外部表相对来说更加安全些，数据组织也更加灵活，方便共享源数据。 

需要注意的是传统数据库对表数据验证是 schema on write（写时模式），而 Hive 在load时是不检查数据是否 
符合schema的，hive 遵循的是 schema on read（读时模式），只有在读的时候hive才检查、解析具体的 
数据字段、schema。 

读时模式的优势是load data 非常迅速，因为它不需要读取数据进行解析，仅仅进行文件的复制或者移动。 
写时模式的优势是提升了查询性能，因为预先解析之后可以对列建立索引，并压缩，但这样也会花费要多的加载时间。

#### hive table directory

hive创建table后指定它的location为hdfs上的某个directory，此时这个表中的所有记录就是该directory下的数据文件，但不包括子目录下的数据文件。而且也无法或者很难配置做到让 hive查询数据的时候读取子目录。

由于这个特点，在输出的时候不能使用子目录。Flink默认的[hdfs connector](https://ci.apache.org/projects/flink/flink-docs-release-1.3/dev/connectors/filesystem_sink.html)使用的是Bucketing File Sink，会按照时间划分bucket，每一个bucket是一个子文件夹，格式是`/base/path/{date-time}/part-{parallel-task}-{count}`这样输出的数据不能被hive读取到。所以要自定义输出文件的目录格式。

自定义的hivesink使用filecount和indexofParallel进行命名，filesize设置为200000条记录。其中在filesize写满文件关闭时才写入磁盘。

另外一个可以注意的地方是以'_'开头的输入文件都会被忽略，这是Hadoop的一个特性。

#### hive导出数据

在终端中执行命令可以直接将sql语句的结果导出为数据文件。

``` bash
hive -e "select * from sjtable where time < '2014-03-12 10:08:33' "> /root/SJ.CSV
```

上面这条语句会将该表sjtable中时间在2014-03-12 10:08:33之前的数据导出到csv文件中。

默认的分隔符是tab\t,可以改成','等符号。

``` bash
hive -e 'select * from some_table' | sed 's/[\t]/,/g' > /home/yourfile.csv
```
