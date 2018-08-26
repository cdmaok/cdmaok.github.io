---
layout: post
title: Spark-tutorial
category: Scala,Java
---
出自[Spark-tutorial](http://spark.apache.org/docs/latest/programming-guide.html)
### RDD Transformation
Transformation | Meaning
---------------| -------
map(func) | 对RDD中的每个元素都进行fun映射
filter(func) | filter operation
flatMap(func) | 扁平的map，map映射后会是一个List(List(E)),而flatMap会是这个List(E)的扁平化， 为List(E),[[1],[2],[3]]{map} -) [1,2,3]{flatMap}
mapPartitions(func) | 类似于map，对每个partition block进行单独的funct映射 func：Iterator(T) => Iterator(U)
mapPartitionsIndex(func) | 同上，带Index, (Int,Iterator(T)) => Iterator(U)
sample(withReplacement,fraction,seed) | 采样
union(otherData) | 并集
interaction(otherData) | 并集
distinct() | 去重
groupbyKey() | (K,V) => (K,Iterator(V))
reducebyKey(func) | (K,V) => (K,V), func: (V,V) => V,意思是funct说明对于同一个Key的Value要进行如何操作
aggregateByKey(zeroValue)(seqOp,combOp) | 聚合操作，zeroValue 初始值，seqOp表示key的切分函数，combOp表示value的合并方式，(K,V) => (K,U)
sortByKey() | sort
join(otherData) | join by Key ( (K,V), (K,W) ) => (K, (V,W))
cogroup(otherData) | ((K,V),(K,U)) => ( K,Iterator(V),(U) )
cartesian(otherData) | 笛卡尔积
pipe(command) | 通过脚本
coalesce(numPartition) | 减少Data的Partition数量
repartition(numPartition) | 重新进行分割
repartitionAndSortWithPartitions(partitioner) | 根据给定partitioner进行分割然后排序

## RDD action
Spark is Lazy，so if the rdd didn't meet with a action, spark didn't compute the transformation at all.  

Actions | Meaning
------- | -------
reduce(func) | func: (V,V) => V, 最后只有一个值
collect() | 相当于合并，打印
count() | size
first() | first element
take(n) | index of n
takeSample(withReplacement,num) | 采样
takeOrdered(n) | 按序输出n
saveAsTextFile(path) | save as text file
saveAsSequenceFile(path) | hadoop sequence file
saveAsObjectFile(path) | serialization saved
countByKey() | (K,V) => Map(K,Integer)
foreach(func) | map

## Spark Streaming  
```Scala
// streaming main function  
updateStateByKey[S: ClassTag](func: (Iterator[(K, Seq[V], Option[S])]) => Iterator[(K, S)])   
// example  
def updateFunction(newValues: Seq[Int], runningCount: Option[Int]): Option[Int] = {  
    val newCount = ...  // add the new values with the previous running count to get the new count  
    Some(newCount)  
}  
val runningCounts = pairs.updateStateByKey[Int](updateFunction _)  
```

### some demo
```python

from pyspark import SparkConf,SparkContext

conf = SparkConf().setMaster('local').setAppName('my app')

sc = SparkContext(conf=conf)

lines = sc.textFile('readme.md')
lines = sc.parallelize(['pandas','i like pandas'])

pythonlines = lines.filter(lambda x:'python' in x)

for _ in pythonlines.take(10):
for _ in pythonlines.top(10):
for _ in pythonlines.takeSample(false,10):
    print _
# 一般来说不能使用collect来代替take函数，应为单机可能无法存储

pythonlines.saveAsTextFile('xxx')
pythonlines.saveAsSequenceFile('xxx')


# aggregate function

sumCount = nums.aggregate((0,0),
    (lambda acc,value:(acc[0] + value,acc[1] + 1)),
    (lambda acc1,acc2:(acc1[0] + acc2[0],acc1[1] +acc2[1])
)
return sumCount[0]/float(sumCount[1])
# 第一个函数是在每个本地节点计算的，分别是累加器和值，第二个函数是对每个节点之后的值进行合并累加器和累加值


# pairRdd
pairs = lines.map(lambda x:(x.split(' ')[0],x))

## 有了pairRdd之后的wordcount
words = lines.flatMap(lambda x: x.split(' '))
result = words.map(lambda x:(x,1)).reducebyKey(x,y:x+y)

## 更直接的方法
lines.flatMap(lambda x: x.split(' ')).countByValue()


## combinebyKey

sumCount = nums.combineByKey((lambda x:(x,1)), # 生成键值
    (lambda x,y:(x[0] + y,x[1] + 1)),      # 单分区内合并
    (lambda x,y:(x[0] + y[0],x[1] + y[1])) # 多分区
    
    )

```


## spark MLlib

```python

from pyspark.mllib.regression import LabeledPoint
from pyspark.mllib.feature import HashingTF
from pyspark.mllib.classification import LogisticRegressionWithSGD

spam = sc.textFile("spam.txt")
normal = sc.textFile("normal.txt")


# 创建一个HashingTF实例来把邮件文本映射为10000维的向量
tf = HashingTF(numFeatures=10000)
spamFeature = spam.map(lambda email: tf.transform(email.split(" ")))
normalFeauture = normal.map(lambda email: tf.transform(email.split(" ")))

# 创建LabeledPoint数据集分别存放正例和反例
positiveExample = spamFeature.map(lambda feature:LabeledPoint(1,feature))
negativeExample = normalFeature.map(lambda feature:LabelPoint(0,feature))
trainingData = positiveExample.union(negativeExample)
trainingData.cache() ## 逻辑回归是迭代算法，缓存训练数据RDD

model = LogisticRegressionWithSGD.train(trainingData)

## 测试
posTest = tf.transform("some text here".split(" "))
negTest = tf.transform("some text here".split(" "))
print "prediction for negative test exmaple " % model.predict(posTest)
print "prediction for postive test example" % model.predict(negTest)


## Vector 基本数据
from numpy import array
from pyspark.mllib.linalg import Vector
vector1 = np.array([1.0,2.0,3.0])
vector2 = Vector([1.0,2.0,3.0])

## pyspark 读取本地文件
val rdd = sc.textFile("file://home/holden/repos/spark/readme.md")

```

## PageRank based on spark
```scala
//假设相邻页面列表以spark objectFile 的形式存储
val links = sc.objectFile[(String,Seq[String])]("links")
                .partitionBy(new HashPartitioner(100))
                .persist()

// 将每个页面的排序值初始化为1.0，由于使用mapvalues生成的rdd的分区将和link一样
var ranks = links.mapValues(v=>1.0)

// 运行10轮迭代
for(i <- 0 until 10){
    val contributions = links.join(ranks).flatMap{
        case (pageId,(links,rank)) =>
            links.map(dest,rank/links.size)
    }
    ranks = contributions.reduceByKey((x,y) => x + y).mapValues(v => 0.5 + 0.85*v)
}

ranks.saveAsTextFile("rank")
```

## spark with hive
```pyspark

from pyspark.sql import HiveContext
hiveCtx = HiveContext(sc)
// 这里可以将读入的文本文件注册成临时表然后进行相关查询操作
row = hiveCtx("select * from someTable where some condition")
firstrow = row.first()
print firstrow.name
```

## spark 共享变量
```python
## 一种是累加器 accumulator,另外一种是广播变量 broadcast variable,以下是两个例子

## python中累加空行,使用累加器，这样写需要有一个全局的变量
file = sc.textFile(inputFile)
blankLines = sc.accumulator(0)

def extractCallSigns(line):
    global blankLines # 访问全局变量
    if line == "":
        blankLines += 1
    return line.split(" ")

callSigns = file.flatMap(extractCallSigns)
print "Blank lines: %d" % blankLines

## 广播变量，所有节点同时读取或者查询一个变量
## python 中查询国家
## 查询RDD contactCounts中的呼号的对应位置，将呼号前缀读取为国家代码来进行查询

signPrefix = loadCallSignTable() ## 普通写法
signPrefix = sc.broadcast(loadCallSignTable())

def processSignCount(sign_count,singPrefix):
    country = lookupCountry(sign_count[0],signPrefix)
    count = sign_count[1]
    return (country,count)

countryContactCounts = (contactCounts.map(processSignCount)
                        .reduceByKey((lambda x,y: x+y)))

## 两种共享变量选择的场景主要是 累加器适合只写场景，广播变量适合只读场景

```

## spark管道 
```python
# 在pyspark中调用外部R脚本，R脚本必须写成类似于hadoop streaming的类型
distScript = '。/src/R/finddistance.R'
distScriptName = 'finddistance.R'
sc.addFile(distScript) ## 分发脚本到各个节点
##还可以使用parallelize进行文件分发。

def somefunction(...):
    return ...

pipeInput = input.map(somefunction)
distance = pipeInput.pipe(SparkFiles.get(distScriptName))
print distance.collect()
```

## spark日志
1. 输出日志可以使用 yarn logs --applicationId <AppId>
2. 默认使用log4j进行日志控制，master会扫描jar包中的log4j.properties文件

## hive & dataframe
```python
input = hiveCtx.jsonFile(inputFilePath)
input.registerTempTable("tweets")
topTweets = hiveCtx.sql("select text,retweetCount from \
    tweets order by retweetCount by retweetCount limit 10")

# topTweets 为DataFrame，由Row对象组成
topTweetsText = topTweets.map(lambda row:row.text)
```

## Parquet 类似于二进制文件
```python

row = hivectx.parquetFile(parquetFilePath)
names = row.map(lambda row:row.name)
names.saveAsParquetFile("hdfs://......")

```

## spark sql udf
```python
hiveCtx.registerFunction("strLenPython",lambda x:len(x),IntegerType())
## 需要定义输出类型
lengthSchemaRdd = hiveCtx.sql("SELECT strLenPython('text') from tweets limit 10")
```

## spark streaming
```java
// 进行流式筛选，打印出包含“error”的行
val scc = new StreamingContext(conf,Second(1))

val lines = scc.socketTextStream("localhost",7777)

var errorlines = lines.filter(_.contain("error"))
errorlines.print()

scc.start() // 启动流计算环境并等待完成
scc.awaitTermination()

// 使用“微批次”的架构，把流式计算当作一系列连续的小规模批处理来对待。
// spark streaming 从各种输入源中读取数据，并把数据分组成小的批次。新的批次按照均匀的时间间隔生成
// 在每个时间区间开始的时候，一个新的批次就创建出来，在一定时间内收到的数据会被添加到这个批次当中。

// 转化操作分成两个部分，分成两个部分，一种是无状态转化，每个批次不依赖之前批次的数据，例如map,reduce,filter等操作，另一种是由状态转化，需要使用之前批次的数据或者是中间结果来计算当前批次的数据。

val logData = ssc.textFileStream("hdfs://.....")
// 使用window()对窗口进行计数
val accessLogsWindow = accessLogDstream.window(Seconds(30),Seconds(10))
val windowCounts = accessLogsWindow.count()

// 对每个IP地址的访问量计算
val ipDStream = accessLogsDStream.map(logEntry => (logEntry.getIpAddress(),1))
val ipCountDStream = ipDStream.reduceByKeyAndWindow(
    {(x,y) => x + y}, // 加上新进入窗口的批次中的元素
    {(x,y) => x - y}, // 移除离开窗口的老批次的元素
    Seconds(30),     // 窗口时长
    Seconds(10)      // 滑动步长
)

```
