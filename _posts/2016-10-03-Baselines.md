---
layout: post
title: Baselines
category: Algorithm
description: Algorithm
---

罗列一下收集的baseLine,不定期更新

## Text process

* Word Embedding
  * sentence2vector   
    * https://github.com/ryankiros/skip-thoughts
    * http://research.microsoft.com/en-us/downloads/731572aa-98e4-4c50-b99d-ae3f0c9562b9/default.aspx
    * http://datascienceassn.org/content/table-xx2vec-algorithms
  * Doc2Vec
    * https://github.com/mchen24/iclr2017

 * glove
    * global vector for word presestation http://nlp.stanford.edu/projects/glove/

  * swivel
    * https://github.com/tensorflow/models/tree/master/swivel

  * Pre-Trained Model 可以直接用
    * https://github.com/Kyubyong/wordvectors

  * parallel word2vec
    * https://github.com/IntelLabs/pWord2Vec

  * xx2vec list
    * https://gist.github.com/nzw0301/333afc00bd508501268fa7bf40cafe4e

* summarization          
    * textteaser  
      * https://github.com/DataTeaser/textteaser
    * pkusumsum
      * http://www.icst.pku.edu.cn/lcwm/wanxj/pkusumsum.htm
    * Consensus-Based-Summarizer
      * https://github.com/ayushoriginal/Consensus-Based-Summarizer

* doc similarity based on word2vec (wmd)
  * https://github.com/mkusner/wmd

* 中文语义相似度计算
  * https://github.com/memect/hao/blob/master/awesome/chinese-word-similarity.md
  * simhash by google

* 中文情感分析
  * https://github.com/memect/hao/issues/113

* 中文词库
  * http://thuocl.thunlp.org/

* nlp 开源工具 刘知远
  * https://www.zhihu.com/question/19929473/answer/90201148?utm_campaign=webshare&utm_source=weibo&utm_medium=zhihu

* SmileMiner
  * 【SmileMiner：国人李海峰开发的开源统计机器智能与学习引擎】 SmileMiner是一个汇集了各种机器学习算法的纯Java函数库，它是自包含的，仅仅需要Java标准库。主要部件为：Smile，-Math，-Data，-Graph，-Interpolation，-NLP。不可多得的Java机器学习库，其文档和示例都挺赞！
https://github.com/haifengl/smile

* 中文句法分析
  * http://www.niuparser.com/

* fasttext
  * https://github.com/facebookresearch/fastText

* nlp 工具
  * https://github.com/aboSamoor/polyglot

* 一些中文常量python lib Zhon is a Python library that provides constants commonly used in Chinese text processing
  * http://zhon.readthedocs.io/en/latest/

* Industrial-strength Natural Language Processing (NLP) with Python and Cython
  * https://github.com/explosion/spaCy

* wiki ner
  * https://github.com/JonathanRaiman/wikipedia_ner

* 词向量预训练模型下载工具
  * https://github.com/chakki-works/chakin#supported-vectors

* Character-level Convolutional Networks for Text Classification
  * https://github.com/zhangxiangxiao/Crepe



## ElasticSearch
  es有段时间没有看了，最近出了好多插件
  * feature vector plugin
    * https://github.com/ginobefun/elasticsearch-feature-vector-scoring
  * learning to rank plugin
    * https://github.com/o19s/elasticsearch-learning-to-rank



## 图像识别
   * Tesseract  https://github.com/memect/hao/blob/master/awesome/ocr-tools.md
   * google vision api
https://cloudplatform.googleblog.com/2015/12/Google-Cloud-Vision-API-changes-the-way-applications-understand-images.html



## Machine Learning

* 快速聚类
  * http://eric-yuan.me/clustering-fast-search-find-density-peaks/
  * https://gist.github.com/jdeng/d2c538e4cab6dd75bf34

* 快速向量搜索工具
  * https://github.com/facebookresearch/faiss

* shortText clustering
  * https://github.com/stephenhky/PyShortTextCategorization

* 大规模数据使用gpu进行knn聚类
  * https://sourceforge.net/projects/gpufsknn/

* dl on spark
  * https://github.com/amplab/SparkNet

* 一些并行算法包括
  * plda，psvm，pspetralcluster,speedo http://openbigdatagroup.github.io/

* GBDT
  * https://github.com/Microsoft/LightGBM

* petuum
  * http://www.petuum.com/

* 概率图模型框架Edward
  * http://edwardlib.org/

## 数据

* 人名识别模型
  * http://www.datatang.com/data/45971

* Datasets for Social Network Analysis
  * https://cn.aminer.org/data-sna

* 中文wordnet
  * http://www.datatang.com/data/46217

* 领域词
  * http://www.datatang.com/data/44347   pullword多的要死

* google的数据集
  * http://svonava.com/post/62186512058/datasets-released-by-google

*  douban movie review
  * http://www.datatang.com/data/46526/

* twitter emoij
  * https://github.com/twitter/twemoji

* bad word list （wash the words）
  * https://git.oschina.net/code_com/badwords

*  语言标注数据 twitter
  * https://blog.twitter.com/2015/evaluating-language-identification-performance

*  yahoo dataset
 * http://webscope.sandbox.yahoo.com/publications.php

* 本文给出了一个观点挖掘的数据集，数据源来自Yahoo问答中与London相关的提问。
  * https://arxiv.org/pdf/1610.03771v1.pdf
* News Quality dataset
  * http://novasearch.org/datasets/

* 电影字幕数据
  * https://nlds.soe.ucsc.edu/fc2
* amazon 评论数据
  * http://jmcauley.ucsd.edu/data/amazon/
* stanford 数据
  * https://snap.stanford.edu/data/


* 股票数据集
  * https://github.com/waditu/Tushare

* 用于过滤敏感词的实用模块
  * https://github.com/observerss/textfilter



## 推荐系统
* 源码 Predict.io


## 工具
* 监控页面变化的开源项目
  * page-monitor https://github.com/fouber/page-monitor

* 抠图工具
  * https://clippingmagic.com/

* 外文书籍
  * http://gen.lib.rus.ec/

* 可视化
  * http://bigdata.memect.com/?tag=survey+visualization
  * http://nanocubes.net/
  * https://policyviz.com/2015/02/17/data-visualization-library/

* 可视化爬虫
  * https://github.com/scrapinghub/portia
  * http://bigdata-madesimple.com/top-50-open-source-web-crawlers-for-data-mining/

* 压力测试工具
  * twitter/iago +VCR(模拟线上请求

* arxiv 代码
  * http://www.gitxiv.com/

* sina 爬虫
  * https://github.com/jinfagang/weibo_terminater

* hadoop 导入工具
  * mysqluldr2

* python 绘图工具
  * https://github.com/pyecharts/pyecharts

* 【可视化降维Python工具包】#Github开源项目# HyperTools 是一个具有可视化功能的降维工具包，其基本流程是输入高维数据，调用降维函数，同时进行绘图。
  * https://github.com/ContextLab/hypertools

* xlearn   High Performance, Easy-to-use, and Scalable Machine Learning Package (C++, Python, R)
  * https://github.com/aksnzhy/xlearn
  
