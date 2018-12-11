---
layout: post
title:  "git报错摘要"
date:   2018-10-18 12:11:02
categories: git
tags: git
excerpt: git error summary
mathjax: true
---

在本地电脑上新建了一个分支，代码写完之后，新关联远程代码库并提交代码：
```
git init
git add .
git commit -m "标注释"
git remote add origin https://gitee.com/fangfu/xxxx.git
```

在执行到git push的时候报了个错：
```
git push
fatal: The current branch master has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin master
```
这个意思是说没有和远程库相关联。
按照提示，推送当前分支并将远程设置为上游，执行git push --set-upstream origin master

这里第一次执行的时候，会弹出输入用户名密码的对话框。如果密码输错了，会报下面这个错误：
```
git push --set-upstream origin master
remote: Incorrect username or password ( access token )
fatal: Authentication failed for 'https://gitee.com/fangfu/xxxx.git/'
```
而且再次执行，不会弹出之前的对话框了。    
这里需要改windows的凭据：   
我是win10系统。点击开始--设置，出现windows设置，直接搜索“凭据管理器”，   
找到普通凭据--编辑刚刚生成的git:https://gitee.com的凭据，修改用户密码就可以了。   
  
如果执行git push过程中出现如下错误：
```
git push --set-upstream origin master
To https://gitee.com/fangfu/xxxx.git
 ! [rejected]        master -> master (fetch first)
error: failed to push some refs to 'https://gitee.com/fangfu/xxxx.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```
大概是说，本地和远程的文件要先合并以后，才能上传新的文件，
所以我们需要先把远程的拉下来
```
git pull origin master
```

如果在执行git pull中报下面的这个错误：
```
git pull origin master
From https://gitee.com/fangfu/xxxx
 * branch            master     -> FETCH_HEAD
fatal: refusing to merge unrelated histories
```
就表示这是两个不同的项目，无法合并，   
如果要合并，需要添加一行代码：--allow-unrelated-histories：   
```
git pull origin master --allow-unrelated-histories
```

执行git clone，报错：
```
error: The requested URL returned error: 401 Unauthorized while accessing https://gitee.com/fangfu/demo.git/info/refs

fatal: HTTP request failed
```
这个问题一般是由于git版本太旧的问题。 
