---
layout: post
title: Word Embedding
category: NLP
---

## 基于矩阵的分布表示  

* 选取上下文，确定矩阵类型  
1. “词-文档”矩阵，非常稀疏  
2. “词-词”矩阵，选取词附近上下文中的各个词（如上下文窗口中的5个词），相对稠密  
3. “词-n gram词组”，选取词附近上下文各词组成的n元词组，更加精准，但是更加稀疏  

* 确定矩阵中各元素的值，包括TF-IDF，PMI，log  
* 矩阵分解，包括 SVD，NMF，CCA，HPCA  

## 基于神经网络的语言模型
* NNLM basic Language model  
![nnlm](http://7xpv97.com1.z0.glb.clouddn.com/6e27cb7b6cc755694077db17bd0f3a29.png)
* LBL 双线性结构
  和nnlm基本类似，只是输出的目标函数稍有不同。
* RNNLM 利用所有的上下文信息，不进行简化（每个词都用上之前看过的所有单词，而不是n个）

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

### word2vec
前面的模型相对而言都很复杂，然后出了一个CBOW和Skip-gram模型。
![skip-gram model](http://7xpv97.com1.z0.glb.clouddn.com/f7190942789f72698eb5593189e2b4e4.png)
没有很多的计算过程，就是根据上下文关系的softmax
