---
layout: post
title: Start Django
category: Web
description: Django 发音 姜戈， 初体验
---

### Motivation

要用到一个中文的情感分析模块snownlp，但是只有python的版本，为了减少重复造轮子的成本，于是用一个python的web框架封装起来，做成一个服务，然后通过web交互完成情感分析，虽然这样部署起来挺麻烦的，但还可以提供给其他模块服务。

### Solution

1. pip install django  
2. 用命令生成Django模板 django-admin startpoject project_name  
4. cd project_name && python manager.py runserver [port] 相当于启动Tomcat容器的意思  
5. 接下来才开始写自己的app. cd project && python manage.py startapp app_name  
6. cd app_name  
 * create app_name/views.py 填写业务逻辑
 * create app_name/urls.py  建立这个逻辑所对应的url
 * 在 project_name/urls.py 把新建立的url添加到容器里面
7. 执行step 4


### TestCase
为了TDD，要写一写testcase,app_name 里面会有tests.py

```
from django.test import TestCase
from myapp.models import Animal

class AnimalTestCase(TestCase):
    def setUp(self):
        Animal.objects.create(name="lion", sound="roar")
        Animal.objects.create(name="cat", sound="meow")

    def test_animals_can_speak(self):
        """Animals that can speak are correctly identified"""
        lion = Animal.objects.get(name="lion")
        cat = Animal.objects.get(name="cat")
        self.assertEqual(lion.speak(), 'The lion says "roar"')
        self.assertEqual(cat.speak(), 'The cat says "meow"')
```

写完之后运行方式和一般的运行方式不一样。应该是用了nose的结果
命令是: python manage.py test app_name  
如果想运行所有的testcase，直接 python manage.py test


### References
1. [Tutorial][Tutorial]


[Tutorial]:https://docs.djangoproject.com/en/1.9/intro/tutorial01/
