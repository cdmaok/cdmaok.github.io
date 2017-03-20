---
layout: post
title: word Embedding
category: nlp
---

一个语言模型的描述
![word-embedding-1](http://7xpv97.com1.z0.glb.clouddn.com/e4f2ceab920fa8692122d09e3d38a853.png)
当m很大的时候没法算，因此做了n-gram的估算
![word-embedding-2](http://7xpv97.com1.z0.glb.clouddn.com/842c3f059e28d74deff8912cc16662af.png)
在计算右边的公式的时候，一般直接采用频率计数的方法进行计算
![word-embedding-3](http://7xpv97.com1.z0.glb.clouddn.com/a3a8bf394bb857bfb85824ef4de65c26.png)
由此带来的问题是数据稀疏问题和平滑问题，由此，神经网络语言模型产生。

Bengio Neural Probabilistic Language Model

![nplm](http://7xpv97.com1.z0.glb.clouddn.com/81301bde23b2843b1a1835410e949ae4.png)

x是一个分布式向量

![distribution](http://7xpv97.com1.z0.glb.clouddn.com/28d9f8c439364f6c190513346c97fcc8.png)
