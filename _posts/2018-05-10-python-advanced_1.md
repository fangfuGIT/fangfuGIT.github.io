---
layout: post
title:  "Python Advanced notes_1"
date:   2018-05-10 15:10:00
categories: Python
tags: python
excerpt: Python Advanced notes_1,a bit of mess
mathjax: true
---

python一切皆对象。  
对象有三个特征：  
1、身份：内存地址  
2、类型：int,float,list..  
3、值：1  
object是最顶层的基类  
type一般用来返回一个类型，同时type也可以生成一个类，  
type本身是一个类，type也是一个对象。typy的父类是object，object的类型是type，包括object、list、str、dict等都是typy的一个实例。  

我们可以通过内置函数hasattr()来判断一个类是否有这个方法，如：  
```
class A:
    pass
cc=A()
print(hasattr(cc,"__init__"))
```

pythn2中使用DFS深度优先算法，python3中使用BFS广度优先算法，
目前python3.6以上使用的是C3算法

mimix类功能单一;
不和基类相关联，可以和任意基类组合;
在mimin类中不要使用super用法;
基类可以不和mimix关联就能初始化成功;
mimix类命令尽量以“mimix”结尾。

注意区分dict的浅拷贝及深拷贝的区别：
```
dict = {"a":{"a1":"a123"},"b":{"b1":"b123"}}

a_dict = dict.copy()
a_dict["a"]["a1"]="newa123"

import copy
b_dict = copy.deepcopy(dict)
b_dict["b"]["b1"]="newb123"
```

list转化成dict,注意get用法:
```
list = ["a1","b1"]
dict_list = dict.fromkeys(list,{"aa1":"1234556"}})
value = dict_list.get("a1")
dict_list.update(a1="new123",b1="bbb123")
dict_list.update((("a1","aaa123"),("b1","bbb123")))
#tuple里面放tuple
```

不建议继承list和dict，在某些情况之下，用c语言写的dict，不会调用我们覆盖的方法,如果实在要继承，可以用UserDict，如：
```
from collections import UserDict
class MyDict(UserDict):
    def __setitem__(self, key, value):
        super().__setitem__(key, value*2)

mydict = MyDict(a=1)
```

set集合frozenset（不可变集合），无序，不重复，如：
```
s=set('abcdcee')
s.add('g')
# ss=frozenset('123')
ss=set('zyxzce')
s.update(ss)
#s.difference(ss)
print(s)
print(ss)
```
frozenset可以做为dict的key

dict查找的性能远大于list，
在list中随着list数据的增大，查找时间会增大，
在dict中查找元素不会随着dict的增大而增大，
dict背后实现原理是哈希表，其特点：
1、dict的key或者set的值，都必须是可以hash的，
就是说对不可变对象都是可hash的，比如str, fronzenset, tuple,
如果是自己实现的类，我们可以在类中加__hash__，这样我们的对象就是可hash对象
2、dict的内存花销大，但是查询速度快，自定义对象或python内部的对象都是用dict包装的。
3、dict的存储顺序和元素添加顺序有关。
4、添加数据有可能改变已有数据的顺序。
当表元小于三分之一，就会重新申请空间，在重新分配内存中，有可能重新改变顺序。

数组与列表dict和list
数组通过偏移量直接寻址
查找原理：
计算键的散列值（哈希值）--> 
使用散列值的一部分来定位散列表中的一个表元 -->
表元为空 --> 抛出keyerror
表元不为空 --> 判断键是否相等 -->
是 --> 返回表元里的值
否，表示散列冲突 --> 使用散列值的另一部分来定位散列表中的另一行 -->
表元为空 --> .....返回前面步骤循环


list[start:end:step]，step默认为1
list的切片操作会返回一个新的列表，切片不会改变list