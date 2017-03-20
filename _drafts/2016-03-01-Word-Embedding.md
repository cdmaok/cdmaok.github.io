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
* LBL 双线性结构
* RNNLM 利用所有的上下文信息，不进行简化
* 
