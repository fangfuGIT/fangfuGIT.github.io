---
layout: post
title:  "xadmin的相关配置及主题修改"
date:   2018-03-15 18:20:00
categories: xadmin
tags: xadmin
excerpt: xadmin的相关配置及主题修改
mathjax: true
---

adminx.py格式：
```
# _*_ coding: utf-8 _*_
__author__ = 'fangfu'
__date__ = '2018/3/15 17:59'

import xadmin
from xadmin import views

from .models import EmailVerifyRecord,Banner

class BaseSetting(object):
    enable_themes = True    #开启主题选项
    use_bootswatch = True   #使用主题

class GlobalSettings(object):
    site_title = u"运维平台"  #定义标题
    site_footer = u"公司"     #定义页脚
    menu_style = "accordion"  #定义菜单缩放 

class EmailVerifyRecordAdmin(object):
    list_display = ['code', 'email', 'send_type', 'send_time']
    search_fields = ['code', 'email', 'send_type']
    list_filter = ['code', 'email', 'send_type', 'send_time']
#参照models，定义菜单

class BannerAdmin(object):
    list_display = ['title', 'image', 'url', 'index', 'add_time']
    search_fields = ['title', 'image', 'url', 'index']
    list_filter = ['title', 'image', 'url', 'index', 'add_time']
#定义菜单

xadmin.site.register(EmailVerifyRecord, EmailVerifyRecordAdmin)
xadmin.site.register(Banner, BannerAdmin)
xadmin.site.register(views.BaseAdminView, BaseSetting)
xadmin.site.register(views.CommAdminView, GlobalSettings)


```

修改users菜单名称：修改users/apps.py
```
添加
    verbose_name = u"用户操作"
```

并修改users/__init__.py
```
default_app_config = "users.apps.UsersConfig"
```

页面无法调出css和js，图片都无法显示
PyCharm报错：
```
    "Your STATICFILES_DIRS setting is not a tuple or list; "
ImproperlyConfigured: Your STATICFILES_DIRS setting is not a tuple or list; perhaps you forgot a trailing comma?
```
把settings.py里的STATICFILES_DIRS改成了[]就ok了

