---
layout: post
title:  ML interview
description: some notes on machine learing interview
categories:
-blog
---

* 介绍一下SVM
  面向数据分类算法，确定一个超平面，从而将不同的数据分割开。 损失函数是hinge Loss

  一个终于让我看懂的blog http://blog.sina.com.cn/s/blog_4298002e010144k8.html


* 介绍一下tensorflow的计算图
  分成两个部分，define and run，构造部分，计算流图，然后通过session执行图中的计算。

* 欧式距离和曼哈顿距离
  欧式距离不解释，曼哈顿距离，假设你在曼哈顿从一个十字路口到另外一个十字路口，驾驶距离不是两个的直线距离，等于各等于各个维度上的差的绝对值的和

* 逻辑回归
  sigmoid 函数，对数损失函数。

* Overfitting
  解决方法 dropout,regularization,batch normalization？及早停止迭代，增加噪音数据，

* LR 和 SVM的区别
  相同：都是分类算法，可以添加各种正则项
  不同：
    - 损失函数不一样，svm是hinge loss，只考虑了分类最相关的少数点,比较不好理解，转化为对偶为题后，只需要计算与少数几个支持向量的距离，简化计算，支持多种核函数，，lr是对数损失函数，好理解。
    - lr 可以给出每个点属于每一个类的概率，svm不可以
    - svm只考虑决策边界附近的点，lr考虑全量的点（更容易受噪音数据影响）

* 决策树，RF，Boosting 和 Adaboost，GBDT和xgboost的区别
  决策树：
    1. 将所有的数据看成一个节点，进入2
    2. 从所有特征中挑选一个特征对节点进行分割，进入3
    3. 生成若干节点，对每个节点进行判断，如果满足停止分裂的条件，进入4，否则进入2
    4. 设置该节点为节点，输出大多数类标号。

  决策树：
  - Cart4.3(使用信息增益率 $$Info_Gain = Entropy / \sum_i (p_i * Entropy_i)$$ + 剪枝)  
  - ID3（使用指标为信息增益 $$Info_Gain = Entropy - \sum_i (p_i * Entropy_i)$$）
  **RF**：包含多个决策树
  **Adaboost**: 自适应增强
  **GDBT**：融合决策树和梯度上升boosting算法,在不破坏原来每颗树的基础上增加模型，但是并不基于全部数据，这样对数据较为敏感。而是改用gradient boost。和rf相比，全是回归树，分类问题的话，将label转成对某一个类的概率值，rf抗噪，多数据格式 
  具体流程如下：
  1. 算法每次生成一个新的决策树
  2. 在每次迭代之前，计算损失函数在数据集上的导数
  3. 通过贪心策略生成新的树（使用了导数作为目标，不是普通的信息增益等），并计算每个叶节点的值
  4. 将新生成的决策树加入到模型中去
   
  **xgboost**:GBDT的提升，损失函数是泰勒展开二项逼近，不是一阶导数，树进行了优化，之所以这样做可以不限定损失函数。  

* bagging && boosting && stacking
  - bagging: 从源数据集中有放回的抽样K个子集，每个子集可以生成一个模型，最后投票得到最终结果，可以并行
  - boosting:每次都是同一个数据集，但是每个样本在新的训练集上的权重会发生改变，每个模型最后的权重不一样，只能串行。
  - stacking: 参考 https://blog.csdn.net/wstcjf/article/details/77989963，进行K折交叉验证，对于同一个模型（例如LR），利用K-1折数据训练，得到剩余1折的数据的预测值，由于交叉验证的原理，最终可以得到一个和训练集label一样大小的预测集（每个样本都会被作为剩余1折中的数据），合并若干个模型的结果（拼接）作为训练样本，同时将原本的测试集上的预测值根据m模型数量取均值后重新生成测试集，进入到下一层的训练中。

* 判别式模型和生成式模型
  判别式：直接通过函数进行判断，
  生成式：学习联合概率密度，然后求条件概率

* l1 和l2正则化
  l1: 因子绝对值之后，使得权值更加稀疏，方便提取特征，符合拉普拉斯分布
  l2: 因子平方和开更号，防止过拟合，符合高斯分布

* CNN works：
  存在局部和整体的关系，由低层次的特征通过组合，得到高级特征并得到不同特征组合的空间关系性。
  主要是局部连接/权值共享/池化操作（取最大或者均值之类的操作）/多层次结构

* lstm 和 rnn
  lstm中包含forget gate，input gate，cell state，hidden information等，lstm是累加形式，rnn是累乘，可以防止梯度消失，或者梯度爆炸，nn隐藏层的输入不仅包括输入层的输出还包括上一时刻隐藏层的输出

* 搜索联想 贝叶斯法则讲述

* plsa 和 lda
  lda是plsa的贝叶斯版本，plsa用极大似然的方法进行求解，lda把主题分布和词语分布弄成随机向量加入dirichlet先验。

* em算法 gmm模型
  e：选取一组参数，求出该参数下隐变量的条件概率值
  m：根据条件概率，推导出联合似然函数的最大值

* 为什么要做归一化
  1.归一化可以提高梯度下降法求解最优解的速度
  2.提高精度，不同特征之间量纲不一样，如果使用距离计算公式会出问题。

* 机器学习流程：
  1.抽象成数学问题，分类还是聚类，
  2.获取数据
  3.特征预处理和特征选择
  4.模型调优和诊断，是否过拟合等等
  5.线上线下融合
  6.上线

* 逻辑回归离散化特征
  工业界很少将连续特征值作为逻辑回归模型的特征输入，而是将连续值离散化，这么做的原因主要是：
  1.离散特征易于添加或者去除
  2.稀疏向量内积快
  3.分桶操作后对异常值有很强的鲁棒性
  4.离散化之后表达能力变强

  * 数据结构分析
  数组：运行速度快，存储效率高，比较适合查找操作
  链表：适合curd，增删改查
  二叉树：具有层次关系的问题

  * 对抗生成网络
  有两方，一方是generator，主要工作是生成图片，另一方是discriminator判断图片是否属于真实样本，双方相互提升。

  * hashtable 和 hashmap
  hashmap基于hashtable实现，hashmap是非同步的，允许null值。


  * deep learning 调参技巧
  1.首先参数初始化，unifrom或者normal高斯分布
  2.数据预处理，进行标准化操作
  3.relu + bn

  * 标准化和归一化区别
  标准化：矩阵 - 均值 / 标准差
  归一化：实质上将最大值映射为1

  * lstm上的sigmoid 和 tanh区别
  二者目的不一样：sigmoid 用在了各种gate上，产生0~1之间的值，这个一般只有sigmoid最直接了。tanh 用在了状态和输出上，是对数据的处理，这个用其他激活函数或许也可以。  @hhhh：另可参见A Critical Review of Recurrent Neural Networks for Sequence Learning的section4.1，说了那两个tanh都可以替换成别的。


  * 梯度消失和梯度爆炸
  消失：根据链式法则，对底层求导=输出对上层求带*权重 联乘，如果由于sigmoid函数的倒数的最大值为1/4，并且w通常会小于1，那么底层的梯度会越来越小，可以通过relu来避免这一情况
  爆炸：如果w在这个时候>>1的话，也会出现这个问题。

  * svd & pca
    PCA是使得数据投影之后方差最大，经过去除均值的操作后，可以用SVD来求解这一向量。

  * 数据不平衡问题
  1.降采样或者过采样
  2.直接加权
  3.修改损失函数
  4.改变评判标准，使用AUC，ROC等
  5.使用bagging，boosting
  6.考虑数据先验分布


  * 深度学习方法脉络
  DNN全连接 --> 解决全连接 CNN，--> 解决时序问题 rnn --> 解决梯度消失问题 --> lstm
  sigmoid会饱和，（对sigmoid 求导的时候基本为0）造成梯度消失。于是有了ReLU
ReLU负半轴是死区，造成梯度变0。于是有了LeakyReLU，PReLU。
强调梯度和权值分布的稳定性，由此有了ELU，以及较新的SELU。
太深了，梯度传不下去，于是有了highway。
干脆连highway的参数都不要，直接变残差，于是有了ResNet。
强行稳定参数的均值和方差，于是有了BatchNorm。
在梯度流中增加噪声，于是有了 Dropout。
RNN梯度不稳定，于是加几个通路和门控，于是有了LSTM。
LSTM简化一下，有了GRU。
GAN的JS散度有问题，会导致梯度消失或无效，于是有了WGAN。
WGAN对梯度的clip有问题，于是有了WGAN-GP。

* 线性分类器
感知准则函数 ：准则函数以使错分类样本到分界面距离之和最小为原则。其优点是通过错分类样本提供的信息对分类器函数进行修正，这种准则是人工神经元网络多层感知器的基础。
支持向量机 ：基本思想是在两类线性可分条件下，所设计的分类器界面使两类之间的间隔为最大，它的基本出发点是使期望泛化风险尽可能小。（使用核函数可解决非线性问题）
Fisher 准则 ：更广泛的称呼是线性判别分析（LDA），将所有样本投影到一条远点出发的直线，使得同类样本距离尽可能小，不同类样本距离尽可能大，具体为最大化“广义瑞利商”。

* bagging & boosting
bagging 均匀采样，各个预测函数没有权重，可以并行
boosting 按照错误率采样，各个预测函数有权重，无法并行。


* HDFS 架构
  - NameNode master节点，管理hdfs的名称空间，管理数据块映射信息，配置副本策略，处理客户端读写请求，保存两份文件，一份是元数据镜像文件，一份是元数据操作日志。
  - DataNode slave 节点，存储实际的读写块，执行数据块读写。
  - Client 文件切分，与NameNode交互，获取文件位置信息，与DataNode交互，读取或者写入数据，管理与访问HDFS
  - Secondary NameNode，并非NameNode的热备份，辅助NameNode，分担其工作量，可辅助恢复NameNode
  - 所有的文件无论大小都被拆成100M左右，为了让所有的机器负载均衡
  - Namenode 中的数据块映射信息block主要是放在内存中，所有的操作日志会持久化，还有定时快照，便于快速恢复，另外二级Namenode会不断的读取载入镜像。
  - 常用命令：
  ```shell
    $ hdfs dfs -usage
    $ hdfs dfs -ls -mkdir -du -df -rm
    $ hdfs dfs -put localfile hadoop_file ,看了一下好像都可以写相对路径，hadoop_file 不需要带hdfs://前缀
    $ hdfs dfs -get src_file dst_file
    $ hdfs dfs -setrep -w 2 xx.file 手动设置xx.file的备份数量
    $ hdfs dfs -fsck xx.file -files -blocks -locations 检查xx.file 在dfs中的文件大小位置
    $ hdfs dfs -getmerge hadoop_file local_file 把hadoop中某个文件夹的内容合成一个文件放到本地
    $ hdfs namenode
    $ hdfs datanode
  ```
  - hdfs中的数据类型大致分成两种，一种是文本，csv,tsv,json,xml等，另外一种是二进制的，例如sequence file（对java友好），arvo（二进制版本json），RCfile，Parquet（列式存储）



* 中文分词
  中文分词的基本方法包括基于语义规则的方法，基于词典的方法，基于统计的方法
  - 基于语义规则的方法需要进行词法分析等，相对的使用较少
  - 基于词典的方法可以进一步划分未最大匹配法，最大概率法，最短路径法。最大匹配法是指按照一定顺序选取字符串中的若干字当作一个词再词典中查找，根据查找方法包括：最大正向匹配，最大反向匹配，双向最大匹配，最小切分等。最大概率是指一个字符串有多种切分方式，取概率最大的一组作为其结果，最短路径是指在词图上选择一条词最少的路径。
  - 基于统计的方法是根据字符串在语料库中出现的统计频率来决定其是否成词，字和字之间共现的频率越高，他们成为词的可信度越高，常见的方法有HMM，MEMM（最大熵隐马尔科夫模型），CRF

不平衡问题中，准确率召回率容易出问题，而由此衍生的都考虑到这个问题。

* xgboost 调参技巧


>> 1. Choose a relatively high learning rate. Generally a learning rate of 0.1 works but somewhere between 0.05 to 0.3 should work for different problems. Determine the optimum number of trees for this learning rate. XGBoost has a very useful function called as “cv” which performs cross-validation at each boosting iteration and thus returns the optimum number of trees required.
>> 2. une tree-specific parameters ( max_depth, min_child_weight, gamma, subsample, colsample_bytree) for decided learning rate and number of trees. Note that we can choose different parameters to define a tree and I’ll take up an example here.
>> 3. Tune regularization parameters (lambda, alpha) for xgboost which can help reduce model complexity and enhance performance.
>> 4. Lower the learning rate and decide the optimal parameters .

1. 在高学习率下把xgboost树的数量顶下
2. 再调树相关的参数，深度，子权重，gamma等
3. 调整正则化参数
4. 缩放学习率


gurobi

1. count 设置一个indicator变量然后求和
2. index 先设置一个indicator 变量，然后 prod range(length) , 然后取min 或者max 值
