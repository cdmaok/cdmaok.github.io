---
layout: post
title: Model-Selection
category: Python
---
## Model-Selection

如果一个模型在不同的测试集上测试结果不仅波动性大，而且预测误差也比较大，就要警惕发生了过拟合现象，此时不妨将模型的复杂度降低些，即使用变量更少的简单模型，比如线性模型  

### 选择模型的方法
  * 重复抽样与预测稳定性角度： CV，GCV，Bootstrap  
  * 似然与模型复杂度角度：AIC、AICc、BIC、EBIC  
  * VC维与风险上界控制角度：SRM  

#### CV
  交叉验证，语料集是{1,2,3,...,N},做成一个K的划分({1,2,3,...,K})，取K中的一份作为测试集，其他作为训练集
  * K = N时称之为"留一法"，每次仅仅观测一个值，那么会造成方差较大，每个训练集和原来的训练集基本无误，，N个模型也是巨大的时间消耗。
  * K << N, 误差较小，每次的训练集相对独立，评价的样本较多，方差较小，但会高估了预测误差。

  TBC









  From [http://cos.name/2015/08/some-basic-ideas-and-methods-of-model-selection/](http://cos.name/2015/08/some-basic-ideas-and-methods-of-model-selection/)
