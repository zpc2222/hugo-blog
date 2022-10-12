---
title: "Nginx"
date: 2022-09-05T00:17:58+08:00
lastmod: 2022-10-12T11:10:00+08:00
author: ["藏锋"]
keywords: 
- jvm
categories: 
- tech
tags: 
- 运维
- nginx
description: "Nginx的一些使用及配置"
weight:
slug: ""
draft: false # 是否为草稿
comments: true
reward: true # 打赏
mermaid: true #是否开启mermaid
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示路径
cover:
    image: "img/nginx.webp" #图片路径例如：posts/tech/123/123.png
    caption: "" #图片底部描述
    alt: ""
    relative: true
---

# Nginx的一些使用心得
## 常用命令
1. 启动

  ```
 进入nginx安装路径下执行start nginx 命令
 
 /usr/sbin/nginx (nginx安装路径) 
 
 service nginx start
```

2. 停止

```
 /usr/sbin/nginx -s stop
 
 nginx -s stop 命令
 
 ps -ef|grep nginx
 
 pkill -9 nginx  (强制停止)
```

3. 重新载入配置/重启

``` 
  sudo /usr/sbin/nginx -s stop ;sudo /usr/sbin/nginx
  
  nginx -s reload
  
  进入nginx可执行目录sbin下，输入命令./nginx -s reload
  
  查找当前nginx进程号，然后输入命令：kill -HUP 进程号 实现重启nginx服务
```

4. 查看启动情况

```
 ps -ef | grep nginx
```
## nginx检查配置
```
nginx -t
```

出现显示nginx.conf 即可
# 配置
```
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
	worker_connections 768;
	# multi_accept on;
}

http {
	##
	# Basic Settings
	##

	sendfile on;
	tcp_nopush on;
	tcp_nodelay on;
	keepalive_timeout 65;
	types_hash_max_size 2048;
	# server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;

	##
	# SSL Settings
	##

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	##
	# Logging Settings
	##

	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	##
	# Gzip Settings
	##

	gzip on;

	# gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

        server {
            listen       80;
            server_name  192.168.1.100;
			client_max_body_size     200m; #上传文件的大小
            proxy_set_header Host $http_host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
			proxy_send_timeout 180s;     # 设置发送超时时间，
	        proxy_read_timeout 180s;      # 设置读取超时时间。
            
	   #web入口工程
           location / {
               root   /home/vue/dist;
               index  index.html index.htm;
               proxy_set_header X-Real-IP $remote_addr;
               proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
               proxy_ssl_session_reuse off;
               charset utf-8;
           }

	    location /admin {
                proxy_set_header Host $host;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
               proxy_pass http://127.0.0.1:8081;
            }
	    
 	    location /app {
                proxy_set_header Host $host;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
               proxy_pass http://127.0.0.1:8080;
            }

	    location /dashboard {
                proxy_set_header Host $host;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
               proxy_pass http://127.0.0.1:9080;
            }


        }  
 

	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;
}

```

**根据上面的nginx配置文件,可以将nginx的配置分为以下的组成结构**
```
... #全局快
events {  #events快
}
http{ #http块
    server { #server快
        location { #location快
        }
    }
}

```
### 每块的结构功能

  (1)、全局块: 配置影响nginx全局的指令。一般有运行nginx服务器的用户组，nginx进程pid存放路径，日志存放路径，配置文件引入，允许生成worker process数等。

  (2)、events块: 配置影响nginx服务器或与用户的网络连接。有每个进程的最大连接数，选取哪种事件驱动模型处理连接请求，是否允许同时接受多个网路连接，  开启多个网络连接序列化等

  (3)、http块: 可以配置多个server,配置代理、缓存、日志定义等能和第三方模块的配置。如文件引入，mime-type定义，日志自定义，是否使用sendfile传输文件，连接超时时间，单连接请求数等。

  (4)、server块: 配置虚拟主机的相关参数，一个http中可以有多个server。

  (5)、location块: 配置请求的路由，以及各种页面的处理情况。
  
 **配置文件中常用的变量,可以用于请求拦截或者匹配路由**
 
> 1.$remote_addr 与 $http_x_forwarded_for 用以记录客户端的ip地址；
2.$remote_user ：用来记录客户端用户名称；
3.$time_local ： 用来记录访问时间与时区；
4.$request ： 用来记录请求的url与http协议；
5.$status ： 用来记录请求状态；成功是200；
6.$body_bytes_s ent ：记录发送给客户端文件主体内容大小；
7.$http_referer ：用来记录从那个页面链接访问过来的；
8.$http_user_agent ：记录客户端浏览器的相关信息；


>参数说明：
1、listen：nginx监听的端口
1、server_name：nginx服务的ip地址或者域名
3、location：配置路由访问信息

>location 配置参数：
1、 = ：用于不含正则表达式的 uri 前，要求请求字符串与 uri 严格匹配，如果匹配
成功，就停止继续向下搜索并立即处理该请求。
2、 ~：用于表示 uri 包含正则表达式，并且区分大小写。
3、 ~*：用于表示 uri 包含正则表达式，并且不区分大小写。
4、 ^~：用于不含正则表达式的 uri 前，要求 Nginx 服务器找到标识 uri 和请求字符串匹配度最高的 location 后，立即使用此 location 处理请求，而不再使用 location块中的正则 uri 和请求字符串做匹配。
注意：如果 uri 包含正则表达式，则必须要有~ 或者 ~*标识。

## proxy_pass带斜杠

proxy_pass末尾有斜杠 / ，proxy_pass不拼接location的路径
```
location /api/ 
{ proxy_pass http://127.0.0.1:8000/; 
}
```
  
请求地址：http://localhost/api/test
转发地址：http://127.0.0.1:8000/test
proxy_pass末尾无斜杠 / ，proxy_pass会拼接location的路径
```
location  /api/ {
proxy_pass http://127.0.0.1:8000;
}
```
 
请求地址：http://localhost/api/test
转发地址：http://127.0.0.1:8000/api/test
## 负载均衡

在 nginx 配置文件中进行负载均衡的配置

1.  在http中添加 upstream ，在upstream 中添加服务列表，设置负载均衡策略
2.  在location 中指定upstream 名称
```
http {
	.....
	
	upstream myservere {
		server 192.168.10.196:8001 weight=1;
		server 192.168.10.196:8002 weight=5;
	}
    server {
        listen       9001;
        server_name  localhost;
		location / {
			proxy_pass http://myservere/;
		}
    }
}
```
## 负载均衡策略

1. 轮询（默认）

每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器 down 掉，能自动剔除。

2. weight

weight 代表权重，默认为 1，权重越高被分配的客户端越多

指定轮询几率， weight 和访问比率成正比，用于后端服务器性能不均的情况。 例如：
```
upstream myservere {
	server 192.168.10.196:8001 weight=1;
	server 192.168.10.196:8002 weight=5;
}
```
 
3. ip_hash

每个请求按访问 ip 的 hash 结果分配，这样每个访客固定访问一个后端服务器，可以解决 session 的问题。 例如：
```
upstream myservere {
	ip_hash;
	server 192.168.10.196:8001;
	server 192.168.10.196:8002;
}
```
  
4. fair（第三方）

按后端服务器的响应时间来分配请求，响应时间短的优先分配。

```
upstream myservere {
	server 192.168.10.196:8001;
	server 192.168.10.196:8002;
	fair;
} 
```


## tips
Linux 服务器上防火墙会端口拦截，所以需要在防火墙中开放80 端口，也可以直接关闭防火墙，这儿提供允许80端口方法

```
# 允许80端口
firewall-cmd --permanent --add-port=80/tcp --zone=public

# 重新加载防火墙配置
firewall-cmd --reload

```