---
layout: post
title: interview
category: interview
---

#### abstract class vs interface

event | abstract class | interface
---| --| --|
method|可以部分有具体实现|不能有具体实现
成员变量| final| non-final
Method keyword| public default| any
keyword | extends | implements
number | multiple | single

#### heap vs stack
stack(栈)，函数调用保存上下文的变量，后进先出
heap，动态分配内存，程序员任意占用或者释放内存，可以变大
application启动时，分配一个heap，application中的thread启动时，每个thread分配一个stack。


#### thread vs process
Processes are the abstraction of running programs.
Threads are the unit of execution in a process
Threads shares memory.


### 二维矩阵的二分查找
在一个二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。
从左下角开始比较，如果大了就向上，小了就向右。另外，不一定需要从左下角开始，右上角开始也可以    
