---
layout: post
title: hadoop note
category: hadoop
---

### memorial

1. HDFS 分块是64MB大小，Job可以对input进行分片，最好是分片和分块一个大小，每个分片上执行一个map任务，如果分片太小要copy到其他机器上执行，浪费io，如果太大肯定慢
2. Map任务将output写出到本地磁盘，而不是HDFS。
3. 使用combiner可以先对mapper输出做一次reduce,每个mapper都有一个combiner,可以减轻reducer的负担
4. hadoop streaming 命令，可以执行python文件
5. hadoop pipes，是hadoop streaming 的 c 版本，需要把文件编译成可执行文件，然后上传到hdfs
6. 读取文件方式：HDFS URL; FileSystem API;
7. 写数据 方式 通过pipe传输  ![hadoop-write-file](http://7xpv97.com1.z0.glb.clouddn.com/29f093a8fc94cbad677fd761e8200fdd.png)
8. 导入工具 Apache Flume(日志导入)，Apache Sqoop(Hive 数据库导入)
9. 常用的数据结构 SequenceFile 适用于日志文件
10. debug工具mrUnit(map reduce unit)
