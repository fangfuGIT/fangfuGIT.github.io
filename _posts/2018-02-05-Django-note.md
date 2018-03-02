---
layout: post
title:  "Django的相关笔记"
date:   2018-02-05 15:20:00
categories: Django
tags: Django
excerpt: 安装配置Django过程的一些记录，持续更新中...
mathjax: true
---

创建一个独立的虚拟环境：
```
pip install virtualenv
```

```
mkdir virtualenv
cd virtualenv/

virtualenv --no-site-packages ffvenv
source ffvenv/bin/activate
```
退出当前虚拟环境：
```
deactivate
```

```
pip install virtualenvwrapper
```


在虚拟环境中安装python3和pip
python下载链接[https://www.python.org/ftp/python/](https://www.python.org/ftp/python/)
```
pip -V
pip install --upgrade pip
pip install Django==1.11.8
```


验证Django:
```
python3
import django
django.get_version()
```

创建一个项目：
```
django-admin.py startproject Djangotest
```

```
pip install pysqlite
yum install sqlite*
```
新建一个应用：
```
python manage.py startapp learn
```

```
systemctl stop firewalld.service
```

在我们创建的项目里修改setting.py文件
ALLOWED_HOSTS = ['*']  ＃在这里请求的host添加了*

```
python manage.py runserver 0.0.0.0:8080
```

参考链接：

[http://www.runoob.com/django/django-tutorial.html](http://www.runoob.com/django/django-tutorial.html)


