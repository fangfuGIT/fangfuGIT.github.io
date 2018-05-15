---
layout: post
title:  "Python Advanced notes_3"
date:   2018-05-15 11:46:00
categories: Python
tags: python
excerpt: Python Advanced notes_3,Iterator、yield
mathjax: true
---

迭代协议，迭代器是访问集合内元素的一种方式，一般用来遍历数据，如：for循环。
迭代器的访问是不能回头的，只能一条一条访问。迭代器提供了一种惰性访问数据的方式。
迭代协议实际上是调用list中的__iter__方法
Iterable 可迭代类型
Iterator 迭代器
比如list是一个可迭代类型，但并不是一个迭代器。
凡是可作用于for循环的对象都是Iterable类型；
凡是可作用于next()函数的对象都是Iterator类型；

只要函数中有yield关键字，就是生成器函数
生成器函数返回的是生成器对象，python编译自解码的时候就产生了对象
惰性求值，延迟求值，

斐波拉契数列0 1 1 2 3 5 8 ...
我们可以通过递归算法算出斐波拉契数列的值，也可以通过下面的代码算出他的执行过程
```
def fib2(num):
    list=[]
    n,a,b = 0,0,1
    while n<=num:
        list.append(b)
        a,b = b,a+b
        n = n+1
    return list
```
好了，我们在通过生成器来实现以上功能：
```
def gen_fib(num):
    n,a,b = 0,0,1
    while n<num:
        yield b
        a,b = b,a+b
        n=n+1

for value in gen_fib(10):
    print(value)
```
在海量数据面前，我们通过生成器，可有效避免浪费内存资源

python解释器会用一个C语言函数PyEval_EvalFramEx去执行

