---
layout: post
title: Python-note
category: Python
---

### 私有成员

```Python
class Student:
    def __init__(self,name,score):
      self.__name = name
      self.__score = score
##使用双下划线表示这个成员变量是私有的，如果外部想access必须通过get，set方法，但如果是__name__表示是特殊变量，不再是私有变量，可以访问。  

## 继承类
# 一个标准类
class Person(object):
  ## ...  continue
class Student(Person):
  ## ...  continue
  ## 方法会直接覆盖点，如果有的话，
## 多重继承
class BirdMan(Person,Runnable):

## 由于python的灵活性，允许动态的给某个类或者某个实例添加方法，为了达到限制的目的，Python允许在定义class的时候，定义一个特殊的__slots__变量，来限制该class能添加的属性：
class Student(object):
  __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称，这一项不对继承的子类起作用。

## 跟slots相类似的一些变量
## __repr__,__str__,前者是用来调试的（在commandline中输入某个实例会直接print），后者是为用户设计的，print xxx
class Student(object):
  def __repr__(self):
      return 'xxxx'
  def __iter__(self):
      ## 相当与java中的iterable的一个迭代器，可以用for xx in object:进行调用
      return self
  def __getitem__(self,n):
      ## 实现类似于list中的get(index) 方法
      return ...

## 附上绑定方法的代码
from types import MethodType
s.set_age = MethodType(set_age, s, Student) # 给实例绑定一个方法，set_age 是刚定义的一个函数，s是Student的一个实例


