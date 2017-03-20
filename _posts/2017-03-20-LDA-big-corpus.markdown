---
layout: post
title:  LDA-big-corpus
description: LDA's speed up and scale up
categories:
- blog
---


本文概括了大规模文本下怎么部署LDA算法，按照timeline进行分析，主要分成两个部分，一个部分是speed up（加速采样算法），一个部分是scale up（加入更大规模数据），主要包含以下算法：

方法|时间复杂度(单词迭代)|瓶颈|	分布式
---| ---| ----| -----
AD-LDA|O(K)|网络延时|是
Async-LDA|O(K)|计算|是
PLDA+|O(K)|计算|是
Yahoo|O(K)|计算|是
Peacock|O(K_d+K_w)|计算|是
FastLDA|<O(K)|计算|否
SparseLDA|O(K_d+K_w)|计算|否
AliasLDA|O(K_d)|计算|否
LightLDA|O(1)|内存|是
WrapLDA|O(1)|内存|是


### Speed up
主要是根据吉布斯采样算法的改进。其中包括FastLDA，SparseLDA,AliasLDA,LightLDA,WarpLDA以上几种。

#### FastLDA
FastLDA的思想在于不想用o(k)的时间去计算归一化参数。每次一计算一个主题的概率，就用一个参数去预估归一化参数Z
![FastLDA-ppt](http://7xpv97.com1.z0.glb.clouddn.com/89e65053d0191305d6c1d7872252e3c8.png)
![FastLDA](http://7xpv97.com1.z0.glb.clouddn.com/b208564de8e26d5cba6b9bd9b1f978ad.png)
这个算法最坏时间复杂度还是o(k)

#### SparseLDA

![SparseLDA-attri](http://7xpv97.com1.z0.glb.clouddn.com/3699d8025f7eaee868a0aef01d96b673.png)  
SparseLDA主要是基于一个假设。在预设主题数目很大时，短文本的主题分布(N_ki)具有稀疏性，同时，词的主题分布(N_kw)具有稀疏性。基于这一结论，SparseLDA重新分解了采样公式1，如公式5所示  

![SparseLDA-formula](http://7xpv97.com1.z0.glb.clouddn.com/41efcdf8486261762ecd3c9ea8eb15bc.png)  
根据计算含义，S为平滑项，R 为document-topic桶，Q为topic-word桶，这些项具有如下性质：S当且仅当超参改变时才会发生变化；R是document-topic桶，我们可以从每篇文档开始采样的时候进行维护，然后在采样过程中做增量更新；对于Q而言，由于稀疏的假设，仅需要计算N_kw≠0的项。整体算法如算法5所示。  
![SparseLDA](http://7xpv97.com1.z0.glb.clouddn.com/757e7a570277166b53225ed72617adc2.png)

根据算法，SparseLDA的时间复杂度根据稀疏情况而定，可表示为O(K_d+K_w)，较标准LDA而言，时间复杂度上要近20倍的提升。然而，当随机初始化主题的时候，往往N_kw并不稀疏并且相对均匀，这个时候往往时间耗费并没有明显的减少。


#### AliasLDA

AliasLDA认为在长度较长的文本中，一个词汇往往会出现在多个主题中，即SparseLDA所描述的词的主题分布稀疏性(N_kw)不一定存在，同时AliasLDA同意文本的主题分布稀疏性(N_ki)这一结论。基于这一原因，AliasLDA重新分解了采样公式。  
![AliasLDA](http://7xpv97.com1.z0.glb.clouddn.com/6fb7c8ba81f0d26eb5cd3bfe60b5218c.png)  
如公式6所示，由于第一项的因子比较稀疏，时间复杂度为O(K_d)，第二项相对稠密，使用了一种新的采样方法，将一个离散分布转换成一个Alias Table ，每次采样耗费O(1)的时间复杂度；此外，AliasLDA不再计算所有主题的概率，而是采用Metropolis-Hasting，根据预设函数（公式7）计算新采样主题和旧主题的转移接收率，判断是否发生转移。如果转移成功则更新主题以及相关统计量，否则使用旧主题。
![AliasLDA-formula](http://7xpv97.com1.z0.glb.clouddn.com/38be5d1936e68dced72766476080674f.png)
构造Alias Table的时间为 O(K)，由于AliasTable可以复用多次，分摊到每个单词上的平均时间复杂度为O(1)，由于假设分布中Q_w 和q_w (t)从AliasTable中得到，只需要O(1)的时间，而
P_ij,p_ij (t)需要耗费O(K_w)的时间复杂度，因而总的时间复杂度为O(K_w)


#### LightLDA
LightLDA在AliasLDA的基础上提出了基于MH的O(1)时间复杂度的采样算法。LighLDA 不认同AliasTable和SparseLDA的假设，认为N_ki和N_kw在规模更大的语料集上并不稀疏。少了这些稀疏性的假设，LightLDA改进的重点是提出了更好的假设分布。根据标准采样公式，LightLDA提出了更为简单的假设分布.  
![LightLDA](http://7xpv97.com1.z0.glb.clouddn.com/7f725d2e80df67ab6e18e68dc7d401b6.png)  
LightLDA将标准采样公式重新分解成两个部分，如公式8，第一项是word-proposal，第二项是doc-proposal。和AliasLDA一样，LightLDA采用AliasTable加Metropolis Hasting 的方法，可以将word-proposal的采样时间复杂度降到O(1)，对doc-proposal采样也采用同样的方法将时间复杂度降到O(1)，此外，由于N_ki=[N_1i,N_2i,…,N_Ki]可以视作一种AliasTable，直接从[1,2,3,..,N_i]中采样出一个数j，取z_ij作为采样的结果。作者认为如果真实分布p中主题k的概率很高，那么假设分布q中的这两项必定有一项在主题k上的概率很高。基于这个原因，在整个迭代采过程中，对每个单词交替使用word-proposal和doc-proposal。

#### WarpLDA
WarpLDA在LightLDA的算法上进行改进，认为可以减少构造AliasTable所消耗O(K)的复杂度，因此不再使用AliasTable，选用了更加细致的假设分布，和更加细致的数据结构来提升缓存的命中率。
WarpLDA给出的假设分布如公式9，和LightLDA相比，仅仅是比LightLDA的word-proposal少了正则化项。WarpLDA给出了如何不构造AlisaTable且在O(1)时间内完成采样的方法。以q^doc为例，和SparseLDA的方法相类似，首先将q^doc分为两项，第一项是doc-topic桶，第二项是smooth桶，根据这两项的正则化项确定在哪一个桶中采样，由于第一项所构成的向量是天然的AliasTable，可以任意取出一个索引，并以该索引对应的值作为采样的结果，而第二项作为平滑项是均匀分布的，可以在O(1)时间内完成。在q^word中为了构造天然的AliasTable，需要以单词为索引构造统计量。  
![warplda](http://7xpv97.com1.z0.glb.clouddn.com/c6a89acecca0c5a769cc5e06bb533c49.png)


#### Summary
FastLDA的实验显示，其迭代逼近归一化参数的方法较标准LDA有5-8倍的提升，然而在最坏情况下，这个算法的时间复杂度仍为O(K)，同时，为了保证尽快的跳出循环，必须让最有可能的主题尽早被计算。采样公式中的各个项需要维护一个有序的数据结构。这一操作需要耗费而外的时间。SparseLDA和AliasLDA都基于LDA主题模型的稀疏性假设减少了计算量。其中SparseLDA假设主题词分布的稀疏性，成功将时间复杂度降至O(K_d+ K_w)；而AliasLDA假设文档主题分布的稀疏性，将时间复杂度降至O(K_d)；他们的缺点都在于对大规模的文本，LDA模型的稀疏性不一定存在。LightLDA和WrapLDA通过调整假设分布，基于Metropolis Hasting采样方法，将平均时间复杂度降至O(1)，其中LightLDA采用了AliasTable索引结构，而WrapLDA利用了更加复杂的数据结构。

### Scale up
大致有以下AD-LDA，Aysnc-LDA,PLDA+,Peacock各种算法。

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
