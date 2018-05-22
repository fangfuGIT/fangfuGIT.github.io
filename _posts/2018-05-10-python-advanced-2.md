---
layout: post
title:  "Python Advanced notes-2"
date:   2018-05-10 18:00:00
categories: Python
tags: python
excerpt: Python Advanced notes-2,metaclass
mathjax: true
---


python中的变量
python的变量实质上是一个指针，包括int、str，
实质上是变量这个指针贴在对象上面：
```
a=[1,2,3]
b=a
b.append(4)
print(a is b)
print(a)
print(b)
print(id(a),id(b))
```
a和b同时指向了相同的list，先生成对象，再贴上变量。

“is”是判断两个对象的id是否相等，“==”判断值是否相等，__eq__
注意特例：python内部有一个优化机制：intern机制，当对象的值为小整数的时候，他们的id也是相等的。

python中的垃圾回收算法是采用 引用计数，
当一个变量指针指向一个对象，对象计数器+1，
```
a = object()
b = a
del a
print(b)
print(a)
```
对象在计数器减到0的时候，才会对对象进行回收
我们可以自定义对象的del方法，在python解释器调用del的时候，执行我们的自定义逻辑：
```
class A:
    def __del__(self):
        pass
```
当把list作为参数，注意没有传参数时的默认值：
```
class Company:
    def __init__(self, name, staffs=[]):
        self.name = name
        self.staffs = staffs

    def add(self, add_name):
        self.staffs.append(add_name)
    def remove(self, del_name):
        self.staffs.remove(del_name).

print(Company.__init__.__defaults__)
```
所以尽量不要把list作为参数进行传递,如果一定要用，注意它的默认值

@property会把一个函数变成一个属性描述符,它属于get方法，如果我们需要set，可以使用setter，如：
```
from datetime import date, datetime

class User:
    def __init__(self, name, birthday):
        self.name = name
        self.birthday = birthday
        self._age = 0

    # def get_age(self):
    #     return datetime.now().year - self.birthday.year

    @property
    def age(self):
        return datetime.now().year - self.birthday.year

    @age.setter
    def age(self, value):
        self._age = value

if __name__ == "__main__":
    user = User("Tom", date(year=1990, month=1, day=1))
    # print(user.get_age())
    user.age = 30
    print("_age is:", user._age)
    print("age is:", user.age)
```

__getattr__：在查找不到类属性的时候会调用，免得抛出异常。
__getattribute__：
在任何情况下，都会无条件的调用这个函数，一般无需去自定义这个函数
如下示例：
```
from datetime import date, datetime

class User:
    def __init__(self, info={}):
        self.info = info

    def __getattr__(self, item):
        return self.info[item]

    # def __getattribute__(self, item):
    #     return

    def get_age(self):
        return datetime.now().year - self.birthday.year

if __name__ == "__main__":
    user = User({"company": "sina", "name": "Cattie", "birthday": datetime(year=1990, month=1, day=1)})
    print(user.info["company"])
    print(user.get_age())
```

关于属性描述符：
如果一个类中有__get__或__set__或__delete__，这个类就可以叫做属性描述符
如果user是某个类的实例，那么user.age等价于getattr(user,"age"),在执行user.age的时候，首先调用__getattribute__,如果类定义了__getattr__方法，那么在__getattribute__抛出AttributeError的时候，就会调用到__getattr__.
而对于属性描述符__get__的调用，则是发生在__getattribute__内部的。
user=User(),那么user.age执行顺序如下：
1、如果age是出现在User或其基类的__dict__中，且age是data descriptor,那么调用data descriptor的get方法，否则执行下一步。
2、如果age出现在user对象的__dict__中，那么直接返回obj.__dict__['age']，否则执行下一步。
3、如果age出现在类User或其基类的__dict__中，则：
   3-1、如果age是non-data descriptor,那么调用non-data descriptor的__get__方法，否则执行下一步
   3-2、返回__dict__['age']
4、如果类User有定义__getattr__,调用__getattr__方法，否则执行下一步
5、抛出异常AttributeError

可以通过代码来理解：
```
import numbers

class Attr:
    #  data descriptor
    def __get__(self, instance, owner):
        return self.value
    def __set__(self, instance, value):
        if not isinstance(value, numbers.Integral):
            raise ValueError("we need some numbers")
        elif value<=0:
            raise ValueError("age can not less than 0")
        elif value>=150:
            raise ValueError("age can not too large")
        self.value = value
    def __delete__(self, instance):
        pass

class NonDataAttr:
    #  non-data descriptor
    def __get__(self, instance, owner):
        return "this is NonDataAttr"

class User:
    age = Attr()           #如果指向数据描述符，则优先返回
    #age = NonDataAttr()   #如果指向非数据描述符，则会在第三步返回

if __name__ == "__main__":
    user = User()
    user.age = 30
    print(user.age)
```

注意区分user.__dict__['age']=30与user.age=30的区别
print(user.__dict__.['age'])与print(user.age)的区别

__new__在前面执行，传递类，可以自定义类的生成过程，在对象生成之前，控制对象的生成过程
__init__在后面执行，传递对象，对对象进行操作，也是用来完善对象的
如果new方法不返回对象，则不会调用init函数
```
class User:
    def __new__(cls, *args, **kwargs):
        print("new")
        return super().__new__(cls)

    def __init__(self, name):
        print("init")
        self.name = name

if __name__ == "__main__":
   # user = User(name="jack")
    user = User("jack")
    print(user.name)
```

type(object_or_name, bases, dict)
type(类名称，基类，属性)
User = type("ddd", (), {})
User = type('ddd', (BaseClass,), {"name": "dd", "saddy":say})
元类是创建类的类，如type

python中类的实例化过程中，会首先寻找metaclass，通过metaclass去创建类，如果没有，则会去基类中寻找metaclass，如果还是没有，则会去模块中找，最后才会去调用type去创建类对象，实例
我们创建类就是为了创建类的实例，我们创建元类就是为了创建类
创建类我们需要定义__new__()函数，用来创建对象并返回方法
可以在类生成对象之前，通过__new__做检查

ORM就是使用元类的例子，把关系数据库的一行映射为一个对象，也就是一个类对应一个表，

关于元类及ORM相关，可以参考下面链接：
[使用元类-廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014319106919344c4ef8b1e04c48778bb45796e0335839000)