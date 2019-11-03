---
layout: post		
title: De-duplicate and similarity calculation		
category: Python		
---

以下简单小结一下去重和相似度计算。  		
去重和相似度计算本质上而言都是查找问题，都是从一个集合里面找出一个子集合，而这个子集合和原来的集合有一定的重复关系，不同的是，**去重** 则是两个元素完全相同，需要进行 **相似度计算** 的，通常是求两个相似的物体。对此分开说明。   		

## 去重		
一般需要做去重计算的都不是文本集合，通常是一些id集合或者数字集合。对于id集合和数字集合，有大概以下的解决方案。  		
* 直接使用语言的特性，将列表(array/list)转换成集合(set)，这样做很快，但是局限于内存，无法忍受大规模数据  		
* 如果id或者数字都是大于0的，可以使用 **bitSet** 进行存储，开辟一个很大的空间，所有的bit都置为0，然后把id或者数字映射成一个整数，把这个整数所对应的bit置为1，这样每次查询对应的bit位即可。详细可看《编程珠玑》  		
* 如果文本集合也想用这种方法，那么就需要做文本到整数的映射，通常使用hash函数（常用的hash函数待补充，這裡有[一點點](http://cdmaok.github.io/java/2015/12/17/Usual-Hash/)），而且通常來說映射成多個整數而不是一个，这就是 **bloom filter** ，这个方法高效，但是会出现误报和漏判的情况。  		
* 还有一种为了加强blomm filter的算法叫 **Cuckoo Filter** ，将一个bitSet分成多个bucket，然后采用hash算法确定bucket的位置，bucket内的位置和备用位置，如果位置有人，就采用备用位置，如果都有人，就把原来的人踢到他的备用位置上面去。具体细节去谷歌。  		

## 相似度计算		
通常为了计算相似度就是为了刨去不仅相同而且部分相似的元素。相似度计算，通常是cos，jaccard，hamming,edit distance，cos最常用，通常而言，这就是一个N*N的矩阵计算， 有可能出现的问题就是语料过大导致的时间过长无法容忍。以下是一些解决方法：        		
  * **倒排索引**，根据元素里的某些特征进行倒排，一般来说没有相同特征的元素相似度基本为0，这部分的计算可以省略不计，如果对时间比较敏感而且相似度要求较低，那么一些普遍的索引项也可以跳过。当然也可以做一个二级的倒排索引进行加速，这种方法不容易漏掉，但是加速效率不高。  		
  * **LSH**，思路基本一致，也是通过hash函数把集合里的元素映射到不同的bucket里面，然后只对bucket里面的元素进行两两的运算，这样可以减少一些运算量，缺点也是会漏报。  		
  * **join**,使用join方法计算，可以解决特征过多的问题,但可能有限制使用的相似度函数。常用的有[passjoin](http://people.csail.mit.edu/dongdeng/),[ppjoinplus](http://code.google.com/p/ppjoinplus)  		

以上，所有的相同元素的求解都可以用相似元素的求解方法进行。另外比较简单粗暴的方法就是加速矩阵的运算，可以并行化，可以直接上cuda，可以用spark，hadoop等集群工具，在此按下不表。		

相似度向量的方法可以参考Item-Measurement-Fucntion这篇文章。		

## 相似文本计算		
以上说的是两个物体转化成向量计算相似度的情况，网上看到quora基于语义计算相似question的[文章](https://www.linkedin.com/pulse/duplicate-quora-question-abhishek-thakur)，记录一下相似文本的相似计算。文中提到了5类特征，其中包括：		
* 基础特征：标题的长度，单词数量		
* 编辑距离：其中包括QRatio,WRatio,Partial Ratio,...,Token sort Ratio		
* TF-IDF距离，SVD降维		
* 基于word2vec的特征，最简单的doc相似度是word embedding之后进行累加求均值，并基于这个的cos距离，曼哈顿距离，jaccard距离等		
* 高级一点的是wmd。		
比较尴尬的最后作者都放弃了这些特征，用了一个大的神经网络去训练。		



### Appendix		
一些比较高级一点的相似度：		
* KL距离，由于KL具有不对称性，所以sim_KL(p,q) = average(KL(p,q) , KL(q,p))
