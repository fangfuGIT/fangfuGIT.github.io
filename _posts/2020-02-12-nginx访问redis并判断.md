---
layout: post
title:  "nginx访问redis并判断"
date:   2020-01-12 09:32:19
categories:  nginx
tags: nginx
excerpt: 
mathjax: true
---


nginx环境及模块的安装部署
http://www.ttlsa.com/nginx/nginx-lua-redis/


80.conf
```
server{
       listen            80;
        root    /data/web;
        index index.html;
        access_log  /var/log/nginx/80.access.log;
        error_log  /var/log/nginx/80.error.log;

    location /get {
           default_type application/json; 
         #  redis2_query auth ${KVSTORE_AUTH};
           set_unescape_uri $key $arg_key;
         #  redis2_query select 0;
           redis2_query get $key;
#          srcache_fetch GET /redis $key; 
          redis2_pass 127.0.0.1:6379;
    }

    location = /redis {
        internal;
        set $redis_key $args;
        redis2_pass 127.0.0.1:6379;
    }

    location /json {
        default_type application/json;
        content_by_lua_file conf/conf.d/nginx.lua;
        error_page 400 = /group/index.html;
    }

    location /group {
        default_type application/json;
        alias /data/web;
        index index.html;
    }

    location ~* /\.(html|htm|jsp|php|js)$ {
        deny all;
     }
}
```



nginx.lua
```
local json = require("json")
local parser = require("redis.parser")
local res = ngx.location.capture("/get",{args = { key = ngx.var.arg_key }})
if res.status == 200 then
reply = parser.parse_reply(res.body)
value = json.encode(reply)
value_decode = json.decode(value)
if ( value_decode == "1" ) then
ngx.exit(400)
else
ngx.exit(403)
end
end
```

