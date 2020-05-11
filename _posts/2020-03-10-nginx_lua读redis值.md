---
layout: post
title:  "nginx_lua读redis值"
date:   2020-03-10 14:29:03
categories:  nginx
tags: nginx
excerpt: 
mathjax: true
---

2月份写的那个方式并不完善。这里重新整理了一下。还是通过nginx_lua读取redis里的值，并根据取出来的值做判断。
用到openresty,更简便。

环境部署
yum install readline-devel pcre-devel openssl-devel
wget https://openresty.org/download/openresty-1.15.8.2.tar.gz
tar zxvf openresty-1.15.8.2.tar.gz
cd openresty-1.15.8.2/
./configure
make
make install

nginx包含在openresty这个包里。


```
server{
       listen          90;
        root    /data/web;
        index index.html;
        access_log  /var/log/nginx/8590.access.log;
        error_log  /var/log/nginx/8590.error.log;


        location /json {
                default_type text/plain;
                content_by_lua_file conf/conf.d/redis.lua;
        } # 主要是这个，后面两个没用。

    location /get {
           default_type application/json; 
#           redis2_query auth ${KVSTORE_AUTH};
           redis2_query auth '123456';
           set_unescape_uri $key $arg_key;
           redis2_query select 0;
           redis2_query get $key;
#          srcache_fetch GET /redis $key; 
          redis2_pass 192.168.253.170:6379;
    }

    location = /redis {
        internal;
        set $redis_key $args;
        redis2_query auth '8k4fc1za-95dq-0pm1';
        redis2_query select 0;
        redis2_pass 192.168.253.170:6379;
    }

    location ~* /\.(html|htm|jsp|php|js)$ {
        deny all;
     }
}

```


vi redis.lua
```
local request_method = ngx.var.request_method
local args = nil
local key = nil
local value = nil
if "GET" == request_method then
        args = ngx.req.get_uri_args()
elseif "POST" == request_method then
        ngx.req.read_body()
        args = ngx.req.get_post_args()
end
key = args["key"]
value = args["value"]
local redis = require "resty.redis"
local cache = redis.new()
local ok, err = cache.connect(cache, '127.0.0.1', '6379')
cache:auth('123456')
cache:select(1)
cache:set_timeout(60000)
if not ok then
        ngx.say("failed to connect:", err)
        return
end
local res, err = cache:get(key)
if not res then
        ngx.say("failed to get "..key..": ", err)
        return
elseif res == ngx.null then
        ngx.say(key.." not found.")
        return
elseif res == "1" then
        ngx.exit(400)
    else
        ngx.exit(403)
end
ngx.say(res)

local ok, err = cache:close()
if not ok then
        ngx.say("failed to close:", err)
        return
end
```



