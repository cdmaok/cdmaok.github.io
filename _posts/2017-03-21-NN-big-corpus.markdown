---
layout: post
title:  Nerual-Network-big-corpus
description: big corpus on nerual network
categories:
- blog
---

本文记录如何在大规模语料上进行神经网络的训练。参考<LDA-big-corpus>这篇文章，要在大规模文本上面训练模型，有两个思路，一个是对模型参数进行分布式，一个是对数据进行分布式处理。
神经网络中最主要的步骤就是使用梯度下降法进行参数的更新。参看了这篇[blog](http://engineering.skymind.io/distributed-deep-learning-part-1-an-introduction-to-distributed-training-of-neural-networks), 但是感觉总结的一般，等后续更新，不过好像也不能超过lda中提出的思想了。  
更新公式类似于
W_ij = W_ij + 学习率 * 某个数据的负梯度


#### Parameter Averaging
这是最简单的策略。如图所示，
![parameter averaging](http://7xpv97.com1.z0.glb.clouddn.com/7dc894da7fa7f3dd22f4aed1789acad6.png)  
在每个计算节点上面备份一个参数矩阵，每个计算节点单独更新自己的参数矩阵上的参数，存储节点上的参数矩阵更新以所有计算节点的参数矩阵的 **均值** 为准。注意存储节点的更新并没有用到更新公式，可以证明是完全符合梯度下降公式的。但是存在的问题在于什么时候更新存储节点上的参数矩阵，如果等到所有计算节点完成一轮迭代，那么可能会受到通讯和最慢机器的性能影响。

#### Aysnchronous SGD
一种自然的思路就是使用异步更新 + 随机梯度下降，跟LDA的思路一毛一样。  
![asyn-sgd](http://7xpv97.com1.z0.glb.clouddn.com/75ace3753e25d236e29f7ccc1e016f2a.png)  
每个计算节点完成一个参数的计算就直接更新到存储节点上去，避免了同步更新的带宽的问题。于此同时带来的问题是读写冲突的问题，假设work1，work2在更新同一个参数W_ij,work1，work2拿到w_ij开始计算梯度，但是由于work1比较慢，work2已经完成了好多次关于w_ij更新，当work1计算完成，向存储节点更新时，这个值其实是不对的，会拖慢收敛速度。其实这个问题加个 **读写锁** 就直接可以解决了，还有人想出了不同性能的work学习度不一样，或者拖慢计算能力快的work的更新程度（soft synchronization等，在异步算法中等一小部分worker一起更新）等方法。


#### Decentralized Aysnchronous SGD
去中心化算法，每个worker之间相互传播，没有一个中心化的节点，据说这样可以缓解带宽的问题。

![decentrailed sgd](http://7xpv97.com1.z0.glb.clouddn.com/7d21877620c31d110342144d5e5f315e.png)
