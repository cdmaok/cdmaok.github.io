---
layout: post
title: cardinality-estimation
category: Algorithm
description: counting,Algorithm
---

简单备忘一下基数估计方法
问题的场景是统计一个海量数据集中不相同元素的个数，就是统计集合的大小--称之为基数  
精准的方法主要有  
* 用分布式平台hadoop之类的进行map之后去重，最后返回，缺点是维度爆炸  
* 使用btree + counter / bitmap + counter    

精确度没有要求的话，最好使用__估算__。

bitmap的缺点是浪费内存，bitmap跟数据集的上限有关，如果是稀疏或者集中在某一个区间的话，很浪费
这里的想法是首先通过hash函数将数据集均匀的分布在一个更小的区间内，然后去计算这个哈希签名的集合基数作为这个数据集基数

业界用的主要是这些
![cardinality counting](http://7xpv97.com1.z0.glb.clouddn.com/0b0db7af93ed10dbd6c4b8b3098c7a1d.png)

稍微展开讲解一下：
1. Linear Counting，首先找个hash function映射到bitmap上面去，然后统计没有被映射到的位置作为基数，讲道理应该不止一个hash function，每个hash 之后的个数取一个均值。据说基数较小的时候效果更好。如图所示：  
![lc-counting](http://7xpv97.com1.z0.glb.clouddn.com/a6a6eeefbd714b0e1a688b80ccfc94f6.png)
2. LogLog Counting 跟前面的一样，hash到bitmap上面去，然后将最长0前缀作为统计量，这样也是会有误差，所以以这样一个统计量作为一个bucket，取均值拿到最后的结果，然后进行2^n计算  
3. Adaptive Counting，就是结合了两种算法，判断了一下两种算法算出来的差距，然后判断应该取信哪一种方法  
4. HyperLoglog，主要是在loglog上面的改进，前面的loglog相当于用了几何平均数，这里用调和平均数来取代他，防止扰动。  
5. HyperLOGLOG++ 是google的一种工程算法，没有理论证明慎用。  

Code  
Java: https://github.com/clearspring/stream-lib  
C/C++: https://github.com/chaoslawful/ccard-lib  




参考[这篇blog](http://blog.codinglabs.org/articles/algorithms-for-cardinality-estimation-part-i.html)
