---
layout: post
title:  "Python笔记"
date:   2018-02-22 15:20:00
categories: Python
tags: Django
excerpt: Python简单笔记，供以后查询使用，持续更新中...
mathjax: true
---

```
pip install virtualenv
```

```
mkdir virtualenv
cd virtualenv/

virtualenv --no-site-packages ffvenv
source ffvenv/bin/activate

如果是windows，去ffvenv目录下找activate，并执行它

```
退出当前虚拟环境：
```
deactivate
```

```
pip install virtualenvwrapper
mkvirtualenv py3virtualenv
workon
deactivate
workon py3virtualenv
```


关于一些函数的记录：
```
capitalize() #首字母大写，其余全部小写 
upper() #全转换成大写
lower() #全转换成小写

高阶函数
map()
reduce()
filter()
sorted()
```
```
d='dxcuUdasx'
print d.capitalize()
```
匿名函数:
```
lamdba[arg1, ..argN]:expression
a=lamdba x, y: x+y
```

装饰器,本质上就是不改变原函数的代码的基础上，为原函数增加新功能的函数,例如：
```
def log(func):
    def inner(*args, **kw):
        print 'log %s():' % func.__name__
        return func(*args, **kw)
    return inner
@log
def f():
    print '2018-03-05'

f()
```

偏函数，适用于较多参数的函数,创建新函数，固定住原函数的部分参数，从而使调用更简单。例如：
```
import functools
int2 = functools.partial(int, base=2)
int2('1000000')
```

类继承，用super(Student, self).__init__(name, gender)去初始化父类:
```
class Person(object):
    def __init__(self, name, gender):
        self.name = name
        self.gender = gender

class Teacher(Person):

    def __init__(self, name, gender, course):
        super(Teacher, self).__init__(name, gender)
        self.course=course

t = Teacher('Alice', 'Female', 'English')
print t.name
print t.course
print isinstance(t, Person)  # 使用isinstance判断类型
```


python 基本语法学习  [https://www.imooc.com/learn/177](https://www.imooc.com/learn/177)

python 函数模块对象  [https://www.imooc.com/learn/317](https://www.imooc.com/learn/317)

