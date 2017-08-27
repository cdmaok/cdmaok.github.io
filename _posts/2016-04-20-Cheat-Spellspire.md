---
layout: post
title: Cheat Spellspire
category: Web
---

### Motivation
遇到一个游戏，手残打不动，请了电脑来虐他。  
![game][game]  
大概是个这样的RPG游戏，每关给出十个字母，每次从十个字母中组合成一定长度并且有意义的单词，形成一定的伤害来打败每一层的怪物。  

### Introduction
由于手残等各种原因，借助代码来完成这个游戏。这个游戏的本质可以认为是列举这十个字母集合中所有子集的全排列，然后在字典中判断这个排列是否存在即可。  
简单的分解表述：1.列举任意长度的子集的全排列；2.在字典中查找这个“排列”是不是单词。

### Solution
分别对上述的两个过程进行处理。  
1. 生成全排列的方法比较简单，直接使用python内置的itertool.permutation生成即可。  
2. 在字典中查找单词的手段比较多，想到过的手段主要有：  
  * 求交集， set(全排列) & set(字典)  
  * 构造字典树，每个排列去查询字典树(串行或者并行)  
  * bloom filter (待尝试)，缺点是容易误报。  

### Experiment



通关了  
![final][final]

### Notice

```python
alist = f(b)
for a in alist:
  do(a)
## change to following one, f(b)可能很大，不要对它进行赋值，直接操作就好。一些比较无脑的优化，但效果挺好
for a in f(b):
  do(a)
```


[game]: http://7xpv97.com1.z0.glb.clouddn.com/spellspire.jpeg
[final]:http://7xpv97.com1.z0.glb.clouddn.com/spellspire-final.png
