---
layout: post
title:  LDA-big-corpus
description: LDA's speed up and scale up
categories:
- blog
---


本文概括了大规模文本下怎么部署LDA算法，按照timeline进行分析，主要分成两个部分，一个部分是speed up（加速采样算法），一个部分是scale up（加入更大规模数据）


### Speed up




### Scale up
大致有以下AD-LDA，Aysnc-LDA

#### AD-LDA
AD-LDA是基于批同步策略的LDA分布式算法，如算法1所示。将一份语料按文档为单位分配到P个处理器上去，每个处理器只负责约1/P 语料的建模工作。由于每个节点上面都是独立完整的文档，所以document-topic矩阵都是准确的，而topic-word矩阵需要将各个处理器上的topic-word矩阵汇总同步。

![ad-lda](http://7xpv97.com1.z0.glb.clouddn.com/dccaffdfeab926ec7b755ce1872c2228.png)

由于topic-word矩阵只有在每轮结束的时候才更新，所以AD-LDA算法是CGS算法的一种近似。作者最后的实验数据验证了这种分布式算法的正确性。由于某些单词不一定在每个节点上存在，这种完全拷贝topic-word矩阵的算法造成了一定的浪费。同时，作者还提出了一种HD-LDA模型，认为每个节点的参数估计应该服从于完整语料的参数的分布，相当于一个混合的LDA模型。**PLDA** 给出了基于HADOOP，MPI等分布式框架的AD-LDA实现，通过借助这些分布式框架，可以有效的保证节点之间的通信和容错性。

#### Async-LDA
Async-LDA 是AD-LDA的异步更新版本，不需要等到所有节点都完成迭代采样之后更新topic-word矩阵，而是提出了一种异步更新topic-word矩阵的方法，将topic-word矩阵分成两个部分，一个部分是处理器p的topic-word矩阵，另外一个矩阵是除去处理器p的topic-word矩阵。

![async-lda](http://7xpv97.com1.z0.glb.clouddn.com/e3bbe5919d9d0f885ff85c30d0c259e0.png)

每个节点p完成采样后，随机和另外一个节点g进行通信，交换各自的采样结果进行对除去当前处理器的全局topic-word矩阵进行增量更新，而增量更新需要节点g之前状态的topic-word矩阵，ASYNC-LDA并没有提供空间来存储这一变量，这一变量是N_wk^(¬p)的一部分，可以通过N ̃_wk^g  ~ MH[N_w^g;N_(w,1)^(¬p),…,N_(w,K)^(¬p)] 采样得到。

#### PLDA+
由于在异步更新策略中，每个节点上面都需要维护一个完整的topic-word矩阵，造成了一定的冗余，对此， PLDA+架构中存在两种节点。如图3所示，一种是P_d，负责进行采样和存储doc-topic矩阵；一种是P_w，负责存储topic-word矩阵。每次的迭代过程如下：
1. P_d向特定的P_w节点请求一个word bundle的topic-word信息
2. P_d完成采样
3. P_d向P_w更新采样结果
为了减少通信，每次都请求一个word bundle的topic-word信息，其中一个word bundle由一个高频词和若干低频词组成的。

![plda+](http://7xpv97.com1.z0.glb.clouddn.com/7755b2199acd513e9c8223f00b88c320.png)

这个看起来就有点像ps的雏形了，PLDA+构造了一个词汇的循环队列以避免读写冲突问题。如图4所示，每个P_d从不同的单词开始，按照循环队列获取单词信息以进行更新。尽管这样，还是存在不同P_d同时请求同一个单词信息的可能。为了加快速度，P_d请求P_w时有超时的机制，一旦等待应答的时间超过设置的时间，则放弃请求，本轮更新放弃对这部分单词的迭代采样。很多后来的机器学习框架都有这个机制，这样认为采样每个隐变量是相互独立的。  
![word-bundle](http://7xpv97.com1.z0.glb.clouddn.com/a5e4a59acd4d4f27e8320a4bd6e18820.png)  

#### Yahoo!LDA
这个比PLDA+更加独立抽象了，这个框架中包含两种类型的节点，一种是计算节点，用于计算（采样），一种是存储节点，用于更新参数（topic-word矩阵），每个计算节点在进行采样的时候，向存储节点通信获取需要的数据，在完成一次采样之后，及时的将采样结果更新到存储节点，由于这期间的读写要求较高，使用memcached 保存，加快响应。计算节点在采样的时候，会对当前采样的单词相关数据申请写锁。由于语料较大造成单词相对稀疏，在采样过程中造成的冲突概率很小。和PLDA+相比，存储节点包括了P_w,P_d两种节点。

#### Peacock
Peacock是腾讯部署一个新的LDA主题模型框架。此前的框架主题数目上限在10^3左右，Peacock将数目提高到10^6左右。相比之下更加抽象化，不仅将语料进行分布式处理，还将参数模型进行分布式处理。  
![peacock](http://7xpv97.com1.z0.glb.clouddn.com/689f71fda9f1b371c372a11f488eed3f.png)
