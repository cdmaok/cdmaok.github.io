---
layout: post
title: SAR-quick-note
category: NLP
---

## introduction

一个比较古老的nlp task  
简单的说就是一个多分类问题，将一句话按照按照目的进行划分，主要可以分成命令,要求,祝願 / 致歉,詢問 / 祈使,邀請,感歎这几种类别。但是后来出现的paper中会有各自的不同，比如说文献[1]分成了5类，其中包括 Statement, Question, Suggestion, Comment, Miscellaneous 这些状态，如下图所示  
![SAR_TYPE](http://7xpv97.com1.z0.glb.clouddn.com/711c197c300a7034fc15240f19a7f5f8.png)  
建模成一个多分类问题之后，可以用ml的方法来解决。接下来就是一些比较有效的特征。

## Features
1. cue words and phrases
2. Non-cue word 包括 是否缩写，观点词，表情，粗俗的话
3. 标点符号



## reference
1. Automatic Twitter Topic Summarization With Speech Acts  
