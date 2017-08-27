---
layout: post
title: Python-hardest-way
category: Python
---

Learn from [hard way to learn python][http://learnpythonthehardway.org/]

Should I use %s or %r for formatting?
You should use %s and only use %r for getting debugging information about something. The %r will give you the "raw programmer's" version of variable, also known as the "representation." **But still, Chinese character need the %s formater**  

What's the difference between input() and raw_input()?
The input() function will try to convert things you enter as if they were Python code, but it has security problems so you should avoid it.

When my strings print out there's a u in front of them, as in u'35'.
That's how Python tells you that the string is Unicode. Use a %s format instead and you'll see it printed like normal.  

How to check some certain function?
example: if we want to read documentation about raw_input, try 'pydoc raw_input' or in python ide, type 'help(raw_input)'

file.truncate([size]) 清空文件或者清除掉指定size的大小。

What does the * in \*args do?  
That tells Python to take all the arguments to the function and then put them in args as a list. It's like argv that you've been using, but for functions. It's not normally used too often unless specifically needed.

Is there any difference between != and <>?  
Python has deprecated <> in favor of !=, so use !=. Other than that there should be no difference.

How do I tell if a number is between a range of numbers?  
You have two options: Use 0 < x < 10 or 1 <= x < 10, which is classic notation, or use x in range(1, 10).

What does result = sentence[:] do?  
That's a Python way of copying a list. You're using the list slice syntax [:] to effectively make a slice from the very first element to the very last one.


```
class Person(object):

    def __init__(self, name):
        ## ??
        self.name = name

        ## Person has-a pet of some kind
        self.pet = None

## ??
class Employee(Person):

    def __init__(self, name, salary):
        ## ?? hmm what is this strange magic?
        super(Employee, self).__init__(name)
        ## ??
        self.salary = salary
```



Avoid multiple inheritance at all costs, as it's too complex to be reliable. If you're stuck with it, then be prepared to know the class hierarchy and spend time finding where everything is coming from.
Use composition to package code into modules that are used in many different unrelated places and situations.
Use inheritance only when there are clearly related reusable pieces of code that fit under a single common concept or if you have to because of something you're using.

python skeleton project
Project-name  
	-- skeleton  
		-- bin  
		-- Name(main module name maybe.)  
			-- __init__.py  
		-- tests  
			-- __init__.py  
			-- test.py  
		-- docs  

		```
    ###setup.py
		try:
			from setuptools import setup
		except:
			from distutils.core import setup
		config = {
			'description': 'My Project',
			'author': 'My Name',
			'url': 'URL to get it at.',
			'download_url': 'Where to download it.',
			'author_email': 'My email.',
			'version': '0.1',
			'install_requires': ['nose'>=1.0],
			'packages': ['NAME'],
			'scripts': [],
			'name': 'projectname'
		}
		setup(**config)
		```


关于测试框架python中的模块是unittest,常见的写法如下：  

```Python
## xx_test.py , this is important,otherwise noestest won't detect it!!!!
import unittest

class TestStringMethods(unittest.TestCase):

  def test_upper(self):
	  self.assertEqual('foo'.upper(), 'FOO')

  def test_isupper(self):
	  self.assertTrue('FOO'.isupper())
	  self.assertFalse('Foo'.isupper())

  def test_split(self):
	  s = 'hello world'
	  self.assertEqual(s.split(), ['hello', 'world'])
	  # check that s.split fails when the separator is not a string
	  with self.assertRaises(TypeError):
		  s.split(2)

if __name__ == '__main__':
	unittest.main()

##有更加高级的框架叫nose，nose继承自unittest，且比unittest更容易使用
## usage，just import the nose module and nose will detect the test function
from nose.tools import *
import NAME

def setup():
    print "SETUP!"

def teardown():
    print "TEAR DOWN!"

def test_basic():
    print "I RAN!"

## 运行方法，如果是unittest，需要手动执行py文件，如果是nose，cd project_name && nosetests.
```



Finally, I'll say that learning to create software changes you and makes you different. Not better or worse, just different. You may find that people treat you harshly because you can create software, maybe using words like "nerd." Maybe you'll find that because you can dissect their logic that they hate arguing with you. You may even find that simply knowing how a computer works makes you annoying and weird to them.

To this I have just one piece of advice: they can go to hell. The world needs more weird people who know how things work and who love to figure it all out. When they treat you like this, just remember that this is your journey, not theirs. Being different is not a crime, and people who tell you it is are just jealous that you've picked up a skill they never in their wildest dreams could acquire.

You can code. They cannot. That is pretty damn cool.
