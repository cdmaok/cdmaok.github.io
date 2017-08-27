---
layout: post
title: Feature-engineering
category: Python
description: 简单的特征处理手段
---
## Feature Enginering

@(Machine Learning)

1. 首先观察数据类型，确定连续，离散，字符，字符串。  
2. 逐一观察变量，统计分布，可使用折线图，箱线图等可视化手段。  
3. 观察变量之间的两两关系。连续变量之间计算相关系数correlation，离散变量之间可以画折线图，使用表格，chi-square test（卡方检验），连续变量和离散变量计算z-test或t-test  
4. 补充缺失数据，希望弄清楚丢失的原因是什么，并以此进行改进。如收入调查时，高低收入者都不太愿意回应，用平均数进行代替并非好的选择。通常的处理手段：  
*	  直接丢失。  
*	  忽略。  
*	  使用中位数，众数，平均数进行填充。  
*	  构造预测模型对缺失值进行预测（可能导致过拟合，或者因为特征之间没有相关性而失效）。  
* 	使用KNN聚类，然后用同一类的数据进行填充。  
5. 检测离群点   
*	  可视化。  
6. 变量转化。  
*	  可观察两两变量之间的关系，尽量将非线性的关系转化为线性的关系。  
*	  可以考虑对特征值取log或square进行修正。  
*	  使用bin分割。  
*	  创造可推导的向量，如将string转成词袋向量，离散值转成二值向量，如3 = [0,0,1]  
7. Scikit-learn provides some useful tools to auto-tune parameters via methods like [Grid Search](http://scikit-learn.org/stable/modules/grid_search.html).  
8. It's generally a good idea to keep the code that creates your model separate from the code that runs it. Use persistance to dump and load the model as needed.  
9. use **cross-validation**  is a commom option.  

![Feature-engineering](http://7xpv97.com1.z0.glb.clouddn.com/637f3c58gw1exd7mcjk7yj28k33uwaoe.jpg)
