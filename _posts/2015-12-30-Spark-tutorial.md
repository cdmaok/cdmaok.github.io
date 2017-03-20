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
