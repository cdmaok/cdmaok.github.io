---
layout: post
title: Optimization-Method
category: Algorithm
description: Algorithm
---

面试的时候疯狂被问到优化问题的求解方法，都答得不是很好，这里组织一下回答。通常这种问题都是在问道__svm__或者__lr__之后开始的。

分类模型中，模型目标函数可以表示成两个部分，一个部分是正则项，一个部分是损失函数。接下来就会对目标函数里面的未知参数进行求解。由于这是个优化问题，不能直接解方程得到，所以就通过迭代的方法进行寻找。优化问题主要有两种方向，一种是一阶导数展开，另外一种就是二阶泰勒展开，分别对应的是__梯度下降系列__以及__牛顿法__系列（LBFGS）

#### GD
梯度下降，求出整批数据的负梯度方向，然后通过迭代参数寻找最优解。
![gd](http://7xpv97.com1.z0.glb.clouddn.com/df47442997736f747fba28ce0ee5d321.png)

#### SGD
加速的方法，每次不求出整批数据的负梯度方向，改为某一个数据的梯度方向，但是迭代的次数就要很多，目前说到的SGD应该是mini-batch GD,对一个子集求负梯度方向
![sgd](http://7xpv97.com1.z0.glb.clouddn.com/058402a8abee7236e5b6a6548c66c287.png)
关于SGD经常被嫌弃的原因还有因为每次得到的梯度可能是噪音，降噪的方法主要有batch(Dynamic Sample Size,每次batch的大小不一样)，随着迭代的进行，batch越来越大（减小bias）,Gradient Aggregation 每次的梯度方向累加之前的梯度方向，其中包括SVGR,SAGA,Iterate Averaging.还有一种改进就是adaptive sampling, 即每次选数据都是根据一个先验分布而不是均一分布

#### Momentum
momentum是模拟物理里动量的概念，积累之前的动量来替代真正的梯度。据说抑制震荡，可以加速收敛的过程。\gamma是一个会变动的参数[0,1]，v是一个大小跟\theta一样的参数，起到累积效果，早期的时候，\gamma设定比较大，这样能保持一致的速度快速下降，中后期的时候，梯度变小了，v是能保持一个较大的值，帮助\theta跳出震荡  
![momentum](http://7xpv97.com1.z0.glb.clouddn.com/658262ce7b334d32f98ea1ae6e28ace0.png)

#### Nesterov
这个是基于动量计算的改进。  
![Nesterov](http://7xpv97.com1.z0.glb.clouddn.com/bfbde7d8ca5bfa7ac62091fdfcd7e237.png)
简单的说就是计算梯度的时候更加细致了，把动量导致的梯度变化也考虑进来。

#### 学习率自适应
其中包括Adagrad，Adadelta，RMSprop，Adam，Adamax之类的方法，一般而言需要设置一个初始值，然后根据前后梯度的变化调整学习率，主要的观点就是如果在前期梯度变化较小，需要一个大的学习率帮助改善，如果后期梯度变化较大，需要一个小的学习率抑制震荡。


接下来是牛顿法系列的解决方案，牛顿法考虑了梯度的梯度，会比梯度下降更容易收敛。
>牛顿法是二阶收敛，梯度下降是一阶收敛，所以牛顿法就更快。如果更通俗地说的话，比如你想找一条最短的路径走到一个盆地的最底部，梯度下降法每次只从你当前所处位置选一个坡度最大的方向走一步，牛顿法在选择方向时，不仅会考虑坡度是否够大，还会考虑你走了一步之后，坡度是否会变得更大。所以，可以说牛顿法比梯度下降法看得更远一点，能更快地走到最底部。

作者：金秉文
链接：https://www.zhihu.com/question/19723347/answer/14636244
来源：知乎
著作权归作者所有，转载请联系作者获得授权。  
简单讲解一下牛顿法，牛顿法一开始是用来求解方程的根的方法，用泰勒展开f(x),然后令f(x) = 0，迭代得到方程最后的解。这个地方看起来和最优化问题求解关系不大，但是转念一想，**最优化问题的求解不就是导函数等于0吗**,所以这里的f(x)是我们目标函数的倒数。

#### Newton Method
![newton method](http://7xpv97.com1.z0.glb.clouddn.com/6ee8bf3fc383c56ae79efcb643a6fe25.png)
其中二阶导称之为Hessian 矩阵，求解这个矩阵相当费时费力。而且还要对这个海森矩阵求逆，简单的方案是在这里使用共轭梯度下降，或者不求完整的Hessian 矩阵，取一个样本子集求Hessian矩阵


#### Hessian-free Newton Method
为了解决牛顿法的问题，这里的解法是对Hessian 矩阵的近似。这里D就是近似Hessian 逆矩阵，g为一阶导数  
![hessian free 1](http://7xpv97.com1.z0.glb.clouddn.com/e973c18aa916d50bd71cfbb24a989389.png)  
![hessian free 2](http://7xpv97.com1.z0.glb.clouddn.com/a3b0d2ce1cd6d1fed597bc05126d7f58.png)  
常见的算法有BFGS，以及LBFGS是bfgs的随机版本。  
![bfgs](http://7xpv97.com1.z0.glb.clouddn.com/01ba97321c2284d096486ec5a7a71624.png)

#### Gauss-Newton
高斯牛顿法是用来近似解决最小二乘法目标函数的方法，首先泰勒展开目标函数，比起直接泰勒展开整个二乘之后的函数，少了一个残差项的二阶偏导数，简化了计算。

#### Natural Gradient
江湖中还有一种专门从概率角度解决优化问题的思路。假设模型参数组成一个空间分布，那么通过DL距离来衡量迭代空间和最优空间的距离，目标函数设置为  
![natural gradient](http://7xpv97.com1.z0.glb.clouddn.com/4cd059ec10c0c8c8edc2b5a094522a36.png)
解法暂时没兴趣看，以后有空补起来。




[参考](https://zhuanlan.zhihu.com/p/22252270)
[一篇综述论文](https://arxiv.org/pdf/1606.04838v1.pdf)
