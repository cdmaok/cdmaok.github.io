---
layout: post
title: Hadoop-Spark-Storm
category: Software
description: Software
---

### Hadoop

主要过程就是：
map:(key1,value1) -> list(key2,value2)
combine: (key2,list(value2)) -> list(key2,value2)
reduce: (key2,list(value2)) -> list(key3,value3)

1. 和其他的相比，主要好处是适合大量的数据，毕竟是通过IO进行交互的。
2. 分成三个模式，一个是本地模式（单进程，没有后台,也没有hdfs），一个是伪分布模式，一个是分布模式
3. debug Method：一种是直接日志打印；设置keep.failed.task.files，可以独立的单独运行这个task；在小的cluster上面跑，让日志更加集中
4. 结构：M/S结果，Master(Namenode,维护所有的文件系统命名空间，TaskTracker)，Slave(DataNode,维护节点中的数据，JobTracker)
5. block 64MB，hdfs不善于管理大量的小文件，主要是hdfs在内存中维护这些文件的地址，会超出内存。
6. Speculative execution 预测执行，一个任务执行到最后阶段的时候，整个任务的执行取决于最慢的那个节点完成最终的执行，这个时候可以进行预测执行，TaskTracker会将剩余的任务分配到空闲的节点上，一旦有节点率先完成了，就会终止其他节点的执行。
7. 容错机制，如果出错了就重启整个task，另外hdfs有备份机制，默认是三个备份，一份在同一个机器上面，一份在同一个机架，另外一份在别的机架上面。
8. fsck hdfs check File system
9. 和RDBMS的区别(简单的说就是量更加大，然后可以做更复杂的分析)
> Traditional RDBMS is used for transactional systems to report and archive the data, whereas Hadoop is an approach to store huge amount of data in the distributed file system and process it. RDBMS will be useful when you want to seek one record from Big data, whereas, Hadoop will be useful when you want Big data in one shot and perform analysis on that later  

10. 动态添加datanode，在master中添加slave配置，然后在slave节点上面打开datanode进程和TaskTracker进程，然后refresh node，可以做到热启动，但是反过来删除节点是在配置文件里面改，然后refresh node
11. secondary NameNode 是 NameNode的一个热备份，但是如果NameNode挂了的话整个system就fail down了，并不是NameNode的一个替代品
12. mapper 产生 kv pair,中间可能有commbier过程，主要是用来解决数据倾斜等问题，主要是一个本地reduce的过程。


### Spark
主要概念就是RDD，可以视作是数据库中的一张表，RDD是不可变的，这一点跟Java中的String类似，可以使用 Transformation 来修改RDD，但这个变化之后生成的是一个新的RDD。
partition 是一个比rdd更小的单位，把rdd分成并行的几个分区，然后并行执行。
1. 和Hadoop比起来的好处在于不需要显式的将任务逻辑转化成mr过程，而且mr过程的输出必须是分布式文件系统，速度很慢，spark允许定义一个DAG的复杂处理逻辑，将中间结果保留在内存当中，
2. RDD的操作包括两种，一种是Transformation,一种是action，前者返回一个rdd集合，后者只返回一个值。

### Storm
storm是一个流式处理系统，主要有三种角色，一种是Nimbus，相当于jobTracker，zookeeper是底层通讯框架，supversior，负责和nimbus交互。supversior负责监控本地的worker，worker有两种角色，一种是spout，一种是bolt，在spout和bolt之间流动的是tuple。每个worker是一个进程，每个executor 是一个线程。


### Kafka
架构图：
![kafka-arch](http://7xpv97.com1.z0.glb.clouddn.com/ad085d362e918ce2e3f54583edab8ca9.png)

多个producer 向不同的broker 发送 message，多个consumer向不同的broker进行message的获取，发送message的时候需要指定topic，partition作为topic的物理分组，可以用多种方法指定。consumer 可以根据自己消费能力控制拉取速度，也可以选择消费模式，比如说批量，重复消费，从尾端开始消费。
