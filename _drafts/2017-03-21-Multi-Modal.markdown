---
layout: post
title:  Multi Modal
description: Multi-modal
categories: blog
---


记一下读过的PDF

>@article{zahavy2016picture,
  title={Is a picture worth a thousand words? A Deep Multi-Modal Fusion Architecture for Product Classification in e-commerce},
  author={Zahavy, Tom and Magnani, Alessandro and Krishnan, Abhinandan and Mannor, Shie},
  journal={arXiv preprint arXiv:1611.09534},
  year={2016}
}

对商品进行快速分类，原本可以视为一个本文分类问题，主要利用的是商品的文本信息，例如title等，但商品的metadata还包括了图片，重量等其他参数。准确的说商品分类应该是文本分类+图片分类+其余参数分类的问题，其实可以采用集成分类的方法进行求解，也可以直接输入文本特征，图片特征和其他特征一锅端，但是本文采用Multi-Modal的方法求解。  

![multi-modal](http://7xpv97.com1.z0.glb.clouddn.com/48b8cfabb897f264fac7cfe9929aa052.png)

论文框架主要是这样，分成两个部分，一个部分是文本特征，文本通过CNN神经网络进行预测，然后Image通过VGG16进行预测，然后合并这两个预测进行最终的预测。
