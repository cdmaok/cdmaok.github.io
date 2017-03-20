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
