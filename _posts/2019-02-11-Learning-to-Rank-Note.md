---
layout: post
title:  Learning to rank
description: Learning to rank
categories: blog
---

learing to rank 主要是IR领域中用来进行二次排序的模块

Ranking 模型可以粗略分为基于相关度和基于重要性进行排序的两大类。 相关度是指query 和 doc之间的相关度，例如（BM25，TF-IDF ），重要性是指 doc本身的重要度，例如pagerank等。

1. 标注方法：
    * 标注相关度等级
    * pairwise标注，doc之间那个更加和query match
    * docs和query之间整体的情况
    三个可以互相转换，例如 pairwise 通过统计某个doc击败了多少其余pair转换成1

2. 统计指标：
    * 给每个query得到的doc一个标签，如果相关为1，不相关为0，MAP(Mean average precision) = （label = 1）的数量 / 总量
    * DCG（Discounted cumulative gain）,累加 相关度 / 位置的信息
    ![20190211101520.png](https://i.loli.net/2019/02/11/5c60dab70f927.png)

    ![20190211101601.png](https://i.loli.net/2019/02/11/5c60dadee4f6d.png)

    NDCG如何理解？对于搜索引擎，本质是用户搜一个query，引擎返回一个结果列表，那么如何衡量这个结果列表的好坏？我能够想到的是：

我们希望把最相关的结果放到排名最靠前的位置，因为大部分用户都是从上往下阅读的，那么最相关的在前面可以最大程度减少用户的阅读时间；
我们希望整个列表的结果尽可能的和query相关；
  第一个条件的满足是首要的，而第二个条件的加入是保证整体结果质量，而这两个条件都体现在了NDCG里面，首先，计算NDCG，需要计算Gain，这个gain即是每条结果的质量的定义，NDCG把所有结果相加最终相加保证，整体质量越高的列表NDCG值越大。同时，Discounted的设计使得越靠前的结果权重越大，这保证了第一条，更相关的排在靠前的结果会有更大的NDCG值。从这两点看，以NDCG为优化目标，保证了搜索引擎在返回结果总体质量好的情况下，把更高质量结果排在更前面。


3. pointwise
  * input: 单个doc和query之间的相关度
  * model: 打分函数根据query和doc进行打分
  * 回归问题： 输出qoc和query之间的得分
  * 分类问题： 1. 无序多分类，建立K-1个分类模型 2. 有序多分类，建立一个打分函数，将一系列得分通过阈值进行分割。
  * 缺点： 1. 只追求排序结果，不追求得分   2. 没有考虑同一个query之间doc的依赖性。3. loss 会被多doc的query放大，被长尾的doc放大
  * 可用的特征：pagerank 得分，query中出现在doc里面的次数


4. pairwise
  * input: 两个doc和query之间的相关度
  * model：比较两个doc在query下的先后顺序
  * 缺点：丢失一部分信息，loss会被多doc的query放大，不考虑位置信息

5. listwise
  * input: 所有doc和query组成的特征乡里
  * output：所有doc的排列
  * model: 打分函数
  * 基于评价指标SoftRank，ApproximateRank，SmoothRank，基于非评价指标




ref
1. https://blog.csdn.net/lipengcn/article/details/80373744 
2. https://www.cnblogs.com/HappyAngel/p/3535919.html
3. https://www.utdallas.edu/~nrr150130/cs6375/2015fa/lects/Lecture_26_Ranking.pdf


