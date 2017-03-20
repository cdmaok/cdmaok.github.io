---
layout: post
title: imbalance-data
category: ML
description: 不平衡数据分类的一些简单方法记载。
---

## introduction

不平衡数据分类
1 1012
0 484
3 160
2 110
5 167
4 398
7 147
labelDict = {'s':0,'c':1,'o':2,'l':3,'t':4,'q':5,'m':6,'p':7}

1.不平衡数据分类对决策树类的分类算法相当不利，
>	First, successive partitioning
>	of the dataspace results in fewer and fewer observations of
>	minority class examples resulting in fewer leaves describing
>	minority concepts and successively weaker confidences
>	estimates. Second, concepts that have dependencies on
>
>	sparseness introduced through partitioning. Here, the first
>	issue correlates with the problems of relative and absolute
>	imbalances, while the second issue best correlates with the
>	between-class imbalance and the problem of high dimensionality. In both cases, the effects of imbalanced data on
>	decision tree classification performance are detrimental.


## solution

主要思路有四种，其中包括降采样，过采样，降采样和过采样结合，集成学习，综述一下。

### 过采样
主要就是增加少数类的样本，直觉就是直接重复少数类样本。可能过拟合。
1. 采用SMOTE算法[46]，和简单的重复少数类样本相比，SMOTE算法选择生成不一样的少数类样本，首先对少数类样本进行聚类，然后选择某个样本和它的类簇中的邻居，生成处于这两个点连线上的样本，填充到数据集中去；![SMOTE](http://7xpv97.com1.z0.glb.clouddn.com/7620ef40432375d4f8baac59e6f15048.png)  
2. Borderline-SMOTE算法[47]是在SMOTE算法上的改进，SMOTE算法在生成少数类样本的过程中对所有少数类样本是一视同仁的，Borderline-SMOTE选择对处于边界位置的少数类样本（近邻中有一半以上的邻居是多数类）生成新样本。
3. SVM-SMOTE 算法[48]同样也是基于Borderline -SMOTE的改进，由于SVM算法是基于寻找决策边界的分类算法，SVM-SMOTE算法首先使用SVM算法确定决策边界，然后对在决策边界附近的样本进行样本的生成，在生成的过程中，如果这个样本是多数类样本，那么在远离这个点和邻居的方向生成新的点，否则在这个点和邻居中间生成点
4. ADASYN算法[49]根据少数类样本分布自适应决定每个少数类样本应该生成的样本数目，并生成不同于原有分布的样本，提高算法的学习能力。ADASYN算法通过根据每个样本周围邻居的标号分布决定这个样本的学习难易程度，并通过难易程度决定要在这个点周围生成多少数据以帮助分类器学习，不仅减少了不平衡数据带来的偏差，还根据数据自适应出样本的学习难易程度进行决策边界的调整。

### 降采样
对多数类进行修剪，使得多数类的样本数量减少，并和少数类的样本数保持一致  
1. 一种方式是通过定义Tomek Link对（如果点x_i和x_j是两个不同类别的点，d(x_i,x_j)表示两点的距离，不存在第三个点x_k，使得d(x_k,x_i )<d(x_i,x_j)或者d(x_k,x_j )<d(x_i,x_j)，那么(x_i,x_j)被称为Tomek Link对，即某个点为噪音或者两个点在类的边界上）的方式，将满足Tomek Link的多数类样本进行剔除[40]；
![data-clean](http://7xpv97.com1.z0.glb.clouddn.com/1f79f5be7c01498e9513f00134979283.png)  
2. 一种方式是对多数类样本进行聚类，对每个类簇中的样本进行删减；
3. 基于聚类的采样方法。和kNN的训练方法类似，首先每个class选一个center，然后每次添加一个样本，并更新所在类的簇中心，直到所有的训练样本都被加进来。这时候开始过采样，往少数类里面添加样本，直到**每个多数类的样本数一样多**以及**所有少数类和多数类的样本数一样多**，在这个图上的反映就是20 + 20 + 20 = 30 + 30。如图所示。  
![cbo](http://7xpv97.com1.z0.glb.clouddn.com/2d114b536f62a70afc71d340cf8a11ab.png)
4. 一种方式是定义NearMiss点[41]（在多数类样本中选择与最近的3个少数类样本平均距离最小的点，在多数类样本中选择与最远的3个少数类样本的平均距离最小的样本和对于每个少数类样本，选择离它最近的给定数量的多数类样本）进行删减；
5. 一种方法是CNN（Condensed Nearest Neighbor）算法[42]，CNN算法寻找一个原始数据集的稳定子集作为平衡数据集。算法使用了两种存储S和T，首先训练集的某一个样本被放入S中，其余样本被放入T中。然后从T中选取某一个样本，通过根据这个点在S中的近邻标签来判断这个点的类标号，如果和真实的类标号不一致，那么将这个样本从T转移到S中。对T中的样本重复上述过程，当没有T中的样本转移到S中去时，算法结束，取S取代原始数据集。CNN算法的思想在于挑出靠近决策边界的样本，去掉一些远离边界的点，帮助细化决策边界，该算法缺点在于对噪音数据十分敏感，容易把噪音视为边界样本；
6. 在One-Sided Selection（OSS）算法[43]中，利用NN决策方法，将所有样本分成以下几类：多数类样本的噪音，类边界样本，安全样本，冗余样本，通过剔除冗余样本，噪音，边界样本的方式降低多数类样本的数量；
7. Neighborhood Cleaning Rule（NCL）[44]的算法和OSS算法类似，但相比之下NCL更加强调数据清洗，除了识别OSS算法中的边界样本之外，NCL算法还通过ENN算法[45]识别T中的噪音样本，并不将这些样本转移到S中去，进一步提高了OSS算法的性能。

### 过采样技术结合降采样技术
主要是前面的过采样技术和降采样技术的罗列组合。相对来说更加平衡，但是操作起来比较复杂
1. SMOTE + Tomek Link
2. SMOTE + ENN

### 集成方法  
1. EasyEnsemble：每次从多数类和少数类中各自采样出一个子集进行训练一个基分类器，最后进行集成学习（无监督）； BalanceCascade 有监督的集成学习，如果某个样本x在某一轮的基分类器中被正确预测了，说明这个样本已经被学习了（冗余），那么在训练下一轮的基分类器的时候就不选择这个样本了。[参考][http://cse.seu.edu.cn/people/xyliu/publication/tsmcb09.pdf]， 另外的采样方法就是首先对多数类的样本进行kNN聚类，通过聚类结果选取合适的多数类样本，选取的方法是选取和少数类样本比较靠近的样本，这样可以比较明确的把边界给确定出来。  



### 代价敏感
1. 在训练模型的目标函数里面加入加重，对少数类被判为多数类的代价进行提升。可以用在Adaboost，DT，NN
2. 对于SVM的模型，有直接修改kernel方法，在kernel里面加入降采样的GSVM-RU


## reference
1. He H, Garcia E A. Learning from imbalanced data[J]. Knowledge and Data Engineering, IEEE Transactions on, 2009, 21(9): 1263-1284.
