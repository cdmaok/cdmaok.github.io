---
layout: post
title: Python-elegant
category: Python
---
This md is to discuss about how to make elegant python code.

### Use Filter

```python
#*---------- Imperative style line selection ------------*#
selected = []                 # temp list to hold matches
fp = open(filename):
for line in fp.readlines():   # Py2.2 -> "for line in fp:"
    if isCond(line):          # (2.2 version reads lazily)
        selected.append(line)
del line                      # Cleanup transient variable

#*-------------use filter here---------------------------------*#
selected = filter(isCond, open(filename).readlines())
# you may write isCond like this:
def isCond(line):
  if cond:
      return True
  return False
# the simple way is to use lambda
isCond = lambda line: cond(line)

```

### 同样的还有map函数，可以修饰代码

### Object-like DataTypes
如果你需要新建一个类，这个类和原有的datatype很像，比如说list，dict，可以直接使用
UserDict，Userlist，UserString这些已经封装好的类进行继承，然后做修改。
