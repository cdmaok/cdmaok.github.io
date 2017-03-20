---
layout: post
title: Usual-Hash
category: Python
description: 常用的哈希函数专题，待补充
---

1. 将一个boolean matrix 转换成一个字符串

```Python
different = [True, False,True,False,False,True,....,True]
# 大概9*8= 72 的列表
# convert the binary array to a hexadecimal String
decimal_value = 0
hex_string = []
for index,value in enumerate(different):
  # True means 1 and False means 0
  if value:
    decimal_value += 2**(index % 8)
    # mod获得列数，二进制的第n列加1
  if (index % 8) == 7:
    # 每行第8列
    hex_string.append(hex(decimal_value)[2:].rjust(2,'0'))
    # 8位2进制转成 2位16进制，然后在左边填充2个‘0’
    decimal_value = 0
    # 重置
return ''.join(hex_string) # 拼接完成

```

https://github.com/MaciejCzyzewski/retter
