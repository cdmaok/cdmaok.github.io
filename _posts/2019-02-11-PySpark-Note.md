---
layout: post
title:  Spark Example
description: spark example
categories: blog
---


```python
from pyspark.sql import SQLContext
from pyspark.sql.types import *

schema = StructType(
    [
        StructField("NodeName",StringType(),True),
        StructField("JingDu",DoubleType(),True),
        StructField("WeiDu",DoubleType(),True)
    ]
)

inputRdd = sc.textFile("file:///xx")
    .map(lambda x:x.split(","))
    .filter(lambda x:len(x) >= 0)
    .map(lambda x:x)

jingweidf = spark.createDataFrame(inputRdd,schema))
jingweidf = sc.broadcast(jingweidf)

hiveCtx = HiveContext(sc)
hiveCtx.sql("use xx")
rows = hiveCtx.sql("select Jnl_Tm,Lgn_Id,GeoInf from .....")

def sth(record,session):
    if condition:
        return (record.NodeName)
    return (record.NodeName,session.Jnl_Tm)


def lookup(session,jingweidf):
    jindu,weidu = session.GeoInf.split("|")
    return jingweidf.map(lambda x:sth(x,session))
                .filter(lambda x:len(x) == 2)


def search(session,jingweidf):

    return jingweidf.map(session,jingweidf.value)


ans = rows.map(search).map(lambda x:(x[0] + ',' + x[1],1))
    .reduceByKey(lambda x,y:x+y)
    .map(lambda x:(x[0].split(',')[0],x[0].split(',')[1],1))


ans.map(lambda x:','.join(ans)).saveAsTextFile('')
```


### Spark 调优指南
1. 尽量避免创建相同的RDD
2. 尽可能复用同一个RDD
3. 对多次使用的RDD进行持久化
4. 尽量避免使用shuffle算子，例如 reduByKey join 等操作
5. 使用map-side的shuffle操作
6. 使用高性能的算子
    * 使用reduceByKey/aggregateByKey 代替 groupByKey
    * 使用mapPartition 代替map
    * 使用foreachPartition 代替foreach
    * 使用filter之后进行cocalesce操作 
        手动减少partition的数量
    * 使用repartitionAndSortWithinPartitions 代替 repartition and sort
        同时进行两个操作
7. 广播大变量
8. 使用Kyro优化序列化性能
9. 优化数据结构

* 资源设置
num-executors:50-100  
executor-memory:4G-8G  
executor-cores:2-4  
driver-memory:4G以内  
spark.default.parallelism:建议设置成 num-executor * executor-cores * 2  
spark.storage.memoryFraction: 持久化的能占的比例  
spark.shuffle.memoryFraction: shuffle 能占的比例  
