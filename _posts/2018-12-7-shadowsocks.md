---
layout: post
title:  "shadowsocks"
date:   2018-12-07 15:46:02
categories:  
tags: 
excerpt: 
mathjax: true
---

先找个那个啥的服务器。    

centos6用pip安装   

没有pip就先装个python3   
python官网：   
https://www.python.org/downloads/   
如：   
```
wget https://www.python.org/ftp/python/3.6.7/Python-3.6.7.tar.xz
tar xvf Python-3.6.7.tar.xz
./configure 
make && make install
cd /usr/bin
mv python pythonbak
ln -s /usr/local/bin/python3 /usr/bin/python
vi /usr/bin/yum

pip3 install shadowsocks

```
vi /etc/shadowsocks.json：
```
{
        "server":"0.0.0.0",
        "port_password":
        {
        "1001":"password",
        "1002":"password",
        "1003":"password",
        "1004":"password"
        },
        "timeout":60,
        "method":"rc4-md5",
        "fast_open":false,
        "workers":1
}
```
ssserver -c /etc/shadowsocks.json -d start   
ssserver -c /etc/shadowsocks.json -d stop    


centos7 可以用脚本进行一键安装：   
```
wget http://morning.work/examples/2015-12/install-shadowsocks.sh   
chmod +x install-shadowsocks.sh     
./install-shadowsocks.sh
```
装完以后需要修改/etc/shadowsocks.json的文件内容   

注意，这个脚本只适用于centos7   


最后打开防火墙中对应的端口比如1001、1002   


搭建这个是工作需要，本文只做笔记，不做解释。   


客户端下载地址：https://github.com/shadowsocks/shadowsocks-windows/releases/download/4.1.5/Shadowsocks-4.1.5.zip
