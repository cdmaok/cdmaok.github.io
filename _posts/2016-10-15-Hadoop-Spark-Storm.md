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
11. secondary NameNode 是 NameNode的一个热备份，但是如果NameNode挂了的话整个system就fail down了，并不是NameNode的一个替代品,理解成checkpoint node,只能恢复到镜像，因此NameNode 是单点失败的
12. mapper 产生 kv pair,中间可能有commbier过程，主要是用来解决数据倾斜等问题，主要是一个本地reduce的过程。
13. 有Job tracker 和 task Tracker，Job Tracker 和 task tracker 通信。 Job Tracker 也是单点失败。
14. 为了解决1.x诸多的单点失败问题，出了YARN（Yet Another Resource Negotiation）
15. MR计算过程中加速的工具包括 combiner，partitioner,comparator. combiner为了在mapper的输出中进行优化，然后才进入reduer； partitioner是为了解决不同reduer之间不同负载的问题，可以指定新的分区方法。comparator是指定排序规则



### Spark
map-reduce 主要是批处理，storm主要是流式处理，spark主要是为了整合批处理，流式处理，图计算，机器学习。
主要概念就是RDD，可以视作是数据库中的一张表，RDD是不可变的，这一点跟Java中的String类似，可以使用 Transformation 来修改RDD，但这个变化之后生成的是一个新的RDD。
partition 是一个比rdd更小的单位，把rdd分成并行的几个分区，然后并行执行。
1. 和Hadoop比起来的好处在于不需要显式的将任务逻辑转化成mr过程，而且mr过程的输出必须是分布式文件系统，速度很慢，spark允许定义一个DAG的复杂处理逻辑，将中间结果保留在内存当中，
2. RDD的操作包括两种，一种是Transformation,一种是action，前者返回一个rdd集合，后者只返回一个值。
3. spark任务执行流向：action -> job -> job stage -> task  
    - 当发现action时，将整个计算过程生成一个job
    - 当job 分解成若干个 job stage 根据是否为 narrow dependency 
    - job stage 分解成若干个task
4. 数据cache原则
    - iterative session, cache preprocess data
    - batch computation, cache dictionary and other dataset that accessed multiple time
    - iterative computation, cache static data
    

### Storm
storm是一个流式处理系统，主要有三种角色，一种是Nimbus，相当于jobTracker，zookeeper是底层通讯框架，supversior，负责和nimbus交互。supversior负责监控本地的worker，worker有两种角色，一种是spout，一种是bolt，在spout和bolt之间流动的是tuple。每个worker是一个进程，每个executor 是一个线程。


### Kafka
架构图：
![kafka-arch](http://7xpv97.com1.z0.glb.clouddn.com/ad085d362e918ce2e3f54583edab8ca9.png)

多个producer 向不同的broker 发送 message，多个consumer向不同的broker进行message的获取，发送message的时候需要指定topic，partition作为topic的物理分组，可以用多种方法指定。consumer 可以根据自己消费能力控制拉取速度，也可以选择消费模式，比如说批量，重复消费，从尾端开始消费。


### YARN
1. TaskTracker -> NodeManager
2. JobTracker -> ResourceManager



Hadoop Streaming Command Line:
```shell
# 通过-file 命令将文件传到每个work节点
yarn jar $HADOOP_STREAMING_JAR -files mapper.py,reducer.sh -mapper 'python mapper.py' -reducer './reducer.sh' -numReduceTasks 1 -input /data/wiki/en_articles -output wc_mr_with_reducer
# 通过-archive 传播文件夹
yarn jar $HADOOP_STREAMING_JAR -files mapper.py,reducer.sh __-archive xx.tar.gz__ -mapper 'python mapper.py' -reducer './reducer.sh' -numReduceTasks 1 -input /data/wiki/en_articles -output wc_mr_with_reducer

## 通过环境变量传参
yarn jar $HADOOP_STREAMING_JAR -files mapper.py,reducer.sh -D variable_name="variable_value" -mapper 'python mapper.py' -reducer './reducer.sh' -numReduceTasks 1 -input /data/wiki/en_articles -output wc_mr_with_reducer

## combiner 和 reducer 类似
yarn jar $HADOOP_STREAMING_JAR -files mapper.py,reducer.sh -D variable_name="variable_value" -mapper 'python mapper.py' -reducer './reducer.sh' -combiner './reducer.sh' -numReduceTasks 1 -input /data/wiki/en_articles -output wc_mr_with_reducer

可以通过
cat xx | mapper_script | sort | reducer_script 进行测试

## 代码推测执行，有时候执行到最后一两个节点会陷入很长的等待，speculation execute(推测执行)可以把剩余的任务切换到其他节点执行,将变量制成true（默认是true）
yarn jar $HADOOP_STREAMING_JAR -D mapreduce.map.speculative="true" \
-D mapreduce.job.speculative-cap-running-tasks=0.99 \
-D mapreduce.job.specualtive-cap-total-tasks=0.99\
-D mapreduce.job.specualtive.retry-after-no-time=10 
-files mapper.py,reducer.sh -D variable_name="variable_value" -mapper 'python mapper.py' -reducer './reducer.sh' -combiner './reducer.sh' -numReduceTasks 1 -input /data/wiki/en_articles -output wc_mr_with_reducer
```

