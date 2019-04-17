---
layout: post
title:  Spark Example
description: spark example
categories:
-blog
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
