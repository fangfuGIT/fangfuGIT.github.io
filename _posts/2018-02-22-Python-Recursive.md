---
layout: post
title:  Recursive Function递归
date:   2018-03-20 10:20:00
categories: Python
tags: Python
excerpt: Python的递归函数
mathjax: true
---

Recursive Function，递归函数，通俗一点说，就是在函数内部调用函数本身。定义递归函数，要先试着去找到让函数终止的条件。函数会通过不断的调用自身，实现算法的目的。递归函数过深的调用会导致栈溢出，这一点要小心。

举例说明：
1+2+3+4+...+100=？
实现代码如下：
```
def digui(b):
    if b==1:
        return 1
    else:
        return b + digui(b-1)
```

其中b==1，就是函数中止的条件。执行调用这个函数，得到结果：
```
print digui(100)
```

再说说汉诺塔吧，汉诺塔起源于印度一个古老传说的益智玩具。
我们对三个柱子分别命名为a, b, c，
a为起始柱，c为目标柱，
整个移动过程可以描述为：
如果a只有一个圆盘，那就直接移动到c，这个是作为函数中止的条件。 
视圆盘有n个，则n=1+(n-1),其中1是一部分，n-1是一部分，先把n-1的部分，移动到b柱，再把剩余的1从a柱移动到c柱，最后再把n-1那一部分，从b柱移到c柱
代码如下：

```
def move(n, a, b, c):
    if n==1:
        print (a,">",c)
        return
    else:
        move(n-1, a, c, b)
        move(1, a, b, c)
        move(n-1, b, a, c)
```
调用并执行这个函数
```
move(4,"a","b","c")
```
可以看到程序会自动打印出整个汉诺塔执行的步骤


