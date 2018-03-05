---
layout: post
title:  "Python笔记"
date:   2018-02-22 15:20:00
categories: Python
tags: Django
excerpt: Python笔记，供以后查询使用，持续更新中...
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







python 基本语法学习  [https://www.imooc.com/learn/177](https://www.imooc.com/learn/177)
python 函数模块对象  [https://www.imooc.com/learn/317](https://www.imooc.com/learn/317)
