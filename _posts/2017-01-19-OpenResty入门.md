---
layout : post
title : OpenResty入门
date : 2017-01-19
author : walkerljl
categories : blog
tag : OpenResty
---

#一、入门
OpenResty是一个基于Nginx与Lua的高性能Web平台，其内集成了大量好的Lua库，第三方模块以及大多数的依赖项。用于方便地搭建能够处理超高并发、扩展性极高的动态Web应用、Web服务和动态网关。

OpenResty通过汇聚各种设计精良的Nginx模块（主要由OpenResty团队自主开发），从而将Nginx有效的变成一个强大的通用的Web应用模块。通过OpenResty，Web开发人员和系统工程师可以使用Lua脚本语言调动Nginx支持的各种C以及Lua模块，快速构造出足以胜任10K乃至1000K以上单机并发连接的高性能Web应用系统。

#二、目标
让你的Web服务直接跑在Nginx服务内部，充分利用Nginx的非阻塞I/O模型，不仅仅对HTTP客户端请求，甚至于对远程后端诸如MySQL、PostgreSQL、Membercached以及Redis等都进行一致的高性能响应。

#三、下载
 [http://openresty.org/cn/download.html](http://openresty.org/cn/download.html)

#四、安装(Mac)
1.安装前的准备

```
$ brew update
$ brew install pcre openssl
```
2.安装

```
$ tar -xzvf openresty-VERSION.tar.gz
$ cd openresty-VERSION/
$ ./configure
$ make
$ sudo make install
```
#五、新手上路
1.创建相关目录

```
$ mkdir ~/openresty
$ cd ~/openresty
$ mkdir logs/ conf/
```
2.准备配置文件conf/nginx.conf

```
worker_processes  1;
error_log logs/error.log;
events {
	worker_connections 1024;
}
http {
	server {
		listen 8080;
		location / {
			default_type text/html;
			content_by_lua '
				ngx.say("<p>hello, world</p>")
          ';
      	}
   }
}
```
3.设置环境变量

```
$ PATH=/usr/local/openresty/nginx/sbin:$PATH
$ export PATH
```
4.启动nginx服务

```
nginx -p `pwd`/ -c conf/nginx.conf
```
5.访问我们的HelloWorld Web服务

```
$ curl http://localhost:8080/
```
#六、基于Redis的动态路由
1.编辑conf/nginx.conf

```
worker_processes  1;
error_log logs/error.log info;

events {
    worker_connections 1024;
}

http {
    upstream apache.org {
        server apache.org;
    }

    upstream nginx.org {
        server nginx.org;
    }

    server {
        listen 8080;

        location = /redis {
            internal;
            set_unescape_uri $key $arg_key;
            redis2_query get $key;
            redis2_pass 127.0.0.1:6379;
        }

        location / {
            set $target '';
            access_by_lua '
                local key = ngx.var.http_user_agent
                local res = ngx.location.capture(
                    "/redis", { args = { key = key } }
                )

                print("key: ", key)

                if res.status ~= 200 then
                    ngx.log(ngx.ERR, "redis server returned bad status: ",
                        res.status)
                    ngx.exit(res.status)
                end

                if not res.body then
                    ngx.log(ngx.ERR, "redis returned empty body")
                    ngx.exit(500)
                end

                local parser = require "redis.parser"
                local server, typ = parser.parse_reply(res.body)
                if typ ~= parser.BULK_REPLY or not server then
                    ngx.log(ngx.ERR, "bad redis response: ", res.body)
                    ngx.exit(500)
                end

                print("server: ", server)

                ngx.var.target = server
            ';

            proxy_pass http://$target;
        }
    }
}
```
2.启动Redis

```
$ ./redis-server  # default port is 6379
```
3.给Redis添加测试数据

```
$ ./redis-cli
redis> set foo apache.org
	OK
redis> set bar nginx.org
   OK
```
4.测试

```
$ curl --user-agent foo localhost:8080
<apache.org home page goes here>

$ curl --user-agent bar localhost:8080
<nginx.org home page goes here>
```
#七、参考资料
- [http://openresty.org/cn/](http://openresty.org/cn/)