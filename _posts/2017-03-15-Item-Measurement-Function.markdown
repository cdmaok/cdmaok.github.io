---
layout: post
title:  "Item Measurement Function"
description: different measurement about items' similarity
categories:
- blog
---


不同的变量有不同的度量方式，简单备忘一下。

* 变量是连续变量，那么很简单，基本上市面上有的度量方式都有用，例如欧氏距离等等。

* 变量是01变量（之所以细分而不直接用离散变量是有讲究的）。首先画一个类似于混淆矩阵的东西，假设有物品i和物品j，有矩阵如下，a表示物品i和物品j中特征全为0的特征个数，以此类推。  

item| 0 | 1
----|---|--
0   | a | b
1   | c | d

  distance(i,j) = (b + c) / (a + b + c + d)
  如果正例和反例的权重不一样，那么也可以 distance(i,j) = (b + c) / (a + b + c) (把d去掉)

*  变量是离散的，但是是多值的情况（例如1234），p是物品的特征总数，m是特征值相等的特征个数  
  distance(i,j) = (p - m) / p

* 变量是多值的，而且是有序的，例如“质量”特征，其特征值为"awful","bad","average","good","excellent"，这些值有相对顺序的，例如dist("excellent","awful")和dist("excellent","average")的相似度应该很不一样。  

   sim(i,j) = 2 * log P(common(A,B)) / log P(description(A,B)) 注意这里是sim不是dist，首先要统计这些特征值的分布  
example：
![ord-1](http://7xpv97.com1.z0.glb.clouddn.com/700127fae3a7d993e8de88b8f2b40d62.png)  

![ord-2](http://7xpv97.com1.z0.glb.clouddn.com/bd11a343fe0ead882aef6d6da98bae6c.png)

* 多种类型变量的组合，可以是不同特征相似度的加权组合。

以上只是给物品相似度定义提供了更多的方法，但是如果想要嵌套在一些常见的ml/dm算法可能还是要映射成一个合理的向量。
