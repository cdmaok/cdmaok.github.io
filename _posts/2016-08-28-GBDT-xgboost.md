---
layout: post
title: GBDT-xgboot
category: ML
description: GBDT,XGBOOST
---

迟迟不敢动手研究GBDT，今天尝试写一下。
主要是ref 这个[xgboost](http://www.52cs.org/?p=429)

首先就是祭出模型的目标函数，主要是分成两个部分，一部分是损失函数，另外一部分是正则化项。
![gbdt-obj](http://7xpv97.com1.z0.glb.clouddn.com/ba324ab50a28dbc162424aff86d026bc.png)

这里面用是的回归树，每一轮的损失函数都是**上一轮的预测值加上这一轮的新树的预测值和真实预测值的残差**，然后定义一个树的正则项（要限制树的复杂程度，一般都是限制叶子节点和深度），然后对obj函数进行二阶泰勒函数展开。

![gbdt-reg](http://7xpv97.com1.z0.glb.clouddn.com/db4e07ae1d24fa90e584a4d330ee3ba5.png)

通过各种推导，可以得到最终的目标函数
![gbdt-obj2](http://7xpv97.com1.z0.glb.clouddn.com/9f458cef79a490ed992e4ca87f4630c3.png)

然后对这个函数并没有使用梯度下降这种解法，因为树的梯度不知道是什么鬼。而是通过枚举别的树，然后计算目标函数，如果ok就作为新一轮的树加入，当然不是全部枚举，而是尝试分割上一轮树的叶子节点，把上一颗树的某个叶子节点再次分裂，方法和普通的rt是一样的从左到右以此尝试阈值就ok了。

有空补一下gbm，rgt
