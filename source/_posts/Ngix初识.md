---
title: Ngix初识
tags:
  - 架构
  - Nginx
categories:
  - 专栏
  - 前端
toc: false
date: 2019-08-01 14:21:35
---

## 一、 什么是Nginx

## 二、 Nginx的功能

- 	作为一个HTTP服务器进行网站的发布处理
- 	高性能的HTTP服务器和反向代理服务器
- 	IMAP、POP3、SMTP代理服务器邮件代理服务器
- 	作为反向代理进行负载均衡
	
	### 2.1 Http和反向代理功能
	
	2.1.1 了解代理概念

	所谓代理就是一个代表、一个通道.在说反向代理之前，我们先看看正向代理，正向代理也是大家最常接触的到的代理模式，我们会从两个方面来说关于正向代理的处理模式，分别从软件方面和生活方面来解释一下什么叫正向代理。

	正向代理的用途：
	（1）访问原来无法访问的资源，如Google
	（2） 可以做缓存，加速访问资源
	（3）对客户端访问授权，上网进行认证
	（4）代理可以记录用户访问记录（上网行为管理），对外隐藏用户信息

	在如今的网络环境下，我们如果由于技术需要要去访问国外的某些网站，此时你会发现位于国外的某网站我们通过浏览器是没有办法访问的，此时大家可能都会用一个操作FQ进行访问，FQ的方式主要是找到一个可以访问国外网站的代理服务器，我们将请求发送给代理服务器，代理服务器去访问国外的网站，然后将访问到的数据传递给我们！
	
	上述这样的代理模式称为正向代理，正向代理最大的特点是**客户端非常明确要访问的服务器地址**；服务器只清楚请求来自哪个代理服务器，而不清楚来自哪个具体的客户端；正向代理模式**屏蔽或者隐藏了真实客户端信息**。

	ps: 客户端必须设置正向代理服务器，当然前提是要知道正向代理服务器的IP地址，还有代理程序的端口(网络链接=>局域网设置=>高级)。

	总结来说：正向代理，"**它代理的是客户端，代客户端发出请求**"，是一个位于客户端和原始服务器(origin server)之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标(原始服务器)，然后代理向原始服务器转交请求并将获得的内容返回给客户端。客户端必须要进行一些特别的设置才能使用正向代理。

	2.1.2 反向代理

	多个客户端给服务器发送的请求，Nginx服务器接收到之后，按照一定的规则分发给了后端的业务处理服务器进行处理了。此时~请求的来源也就是客户端是明确的，但是请求具体由哪台服务器处理的并不明确了，Nginx扮演的就是一个反向代理角色。

	客户端是无感知代理的存在的，反向代理对外都是透明的，访问者并不知道自己访问的是一个代理。因为客户端不需要任何配置就可以访问。

	反向代理，"它代理的是服务端，代服务端接收请求"，主要用于服务器集群分布式部署的情况下，反向代理隐藏了服务器的信息。

	反向代理的作用：
	（1）保证内网的安全，通常将反向代理作为公网访问地址，Web服务器是内网
	（2）负载均衡，通过反向代理服务器来优化网站的负载

	
	2.1.3 正反代理区别

	图解:

	

	
- 	在正向代理中，Proxy和Client同属于一个LAN（图中方框内），隐藏了客户端信息；
- 
- 	在反向代理中，Proxy和Server同属于一个LAN（图中方框内），隐藏了服务端信息；
- 
- 	实际上，Proxy在两种代理中做的事情都是替服务器代为收发请求和响应，不过从结构上看正好左右互换了一下，所以把后出现的那种代理方式称为反向代理了。

### 2.2 负载均衡

	Nginx扮演了反向代理服务器的角色，它是以依据什么样的规则进行请求分发的呢？不用的项目应用场景，分发的规则是否可以控制呢？

	这里提到的客户端发送的、Nginx反向代理服务器接收到的请求数量，就是我们说的负载量。

	请求数量按照一定的规则进行分发到不同的服务器处理的规则，就是一种均衡规则(又称负载均衡策略)。

	所以~将服务器接收到的请求按照规则分发的过程，称为负载均衡。

	负载方式:

- 	硬件负载(例如F5负载策略)
- 	软件负载(软件层的消息队列分发机制)

	Nginx支持的负载均衡调度算法方式如下：

- 	weight轮询(默认，常用)：接收到的请求按照权重分配到不同的后端服务器，即使在使用过程中，某一台后端服务器宕机，Nginx会自动将该服务器剔除出队列，请求受理情况不会受到任何影响。 这种方式下，可以给不同的后端服务器设置一个权重值(weight)，用于调整不同的服务器上请求的分配率；权重数据越大，被分配到请求的几率越大；该权重值，主要是针对实际工作环境中不同的后端服务器硬件配置进行调整的。
- 	ip_hash（常用）：每个请求按照发起客户端的ip的hash结果进行匹配，这样的算法下一个固定ip地址的客户端总会访问到同一个后端服务器，这也在一定程度上解决了集群部署环境下session共享的问题。
- fair：智能调整调度算法，动态的根据后端服务器的请求处理到响应的时间进行均衡分配，响应时间短处理效率高的服务器分配到请求的概率高，响应时间长处理效率低的服务器分配到的请求少；结合了前两者的优点的一种调度算法。但是需要注意的是Nginx默认不支持fair算法，如果要使用这种调度算法，请安装upstream_fair模块。
- 	url_hash：按照访问的url的hash结果分配请求，每个请求的url会指向后端固定的某个服务器，可以在Nginx作为静态服务器的情况下提高缓存效率。同样要注意Nginx默认不支持这种调度算法，要使用的话需要安装Nginx的hash软件包。


## 三、 Nginx与其他服务器的性能比较

|服务器|Apache|Nginx|
|-|-|-|
|Proxy代理|非常好|非常好|
|Rewriter|好|非常好|
|Fcgi|不好|好|
|热部署|不支持|支持|
|系统压力|很大|很小|
|稳定性|好|非常好|
|安全性|好|一般|
|静态文件处理|一半|非常好|
|反向代理|一般|非常好|


## 四、 Nginx配置文件的编写、解析
	
### 4.1 如何使用Nginx

	4.1.1 Nginx安装

 	4.1.2 Nginx配置

	安装完成之后，配置目录 conf 下有以下配置文件，过滤掉了 xx.default 配置：

```

ubuntu: /opt/nginx-1.7.7/conf$ tree |grep -v default
.
├── fastcgi.conf
├── fastcgi_params
├── koi-utf
├── koi-win
├── mime.types
├── nginx.conf
├── scgi_params
├── uwsgi_params
└── win-utf
```

	nginx.conf 是主配置文件，默认配置去掉注释之后的内容如下图所示：

```

worker_process      # 表示工作进程的数量，一般设置为cpu的核数

worker_connections  # 表示每个工作进程的最大连接数

server{}            # 块定义了虚拟主机

    listen          # 监听端口

    server_name     # 监听域名

    location {}     # 是用来为匹配的 URI 进行配置，URI 即语法中的“/uri/”

    location /{}    # 匹配任何查询，因为所有请求都以 / 开头

        root        # 指定对应uri的资源查找路径，这里html为相对路径，完整路径为
                    # /opt/nginx-1.7.7/html/

        index       # 指定首页index文件的名称，可以配置多个，以空格分开。如有多
                    # 个，按配置顺序查找。
```

	真实用例,小编自己ng安装路径下的nginx.config配置文件,默认如下:

```

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
	    
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
	# proxy_pass 后面跟着一个 URL，用来将请求反向代理到 URL 参数指定的服务器上
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}

```

	从配置可以看出，Nginx 监听了 80 端口、域名为 localhost、根路径为 html 文件夹（我的安装路径为 /opt/nginx-1.7.7，所以 /opt/nginx-1.7.7/html）、默认 index 文件为 index.html，index.htm 服务器错误重定向到 50x.html 页面。

	可以看到 /opt/nginx-1.7.7/html/ 有以下文件：

```language

shuaiqidewuduzaideMacBook-Pro:html runing_liu$ ls -a
.		..		50x.html	index.html
shuaiqidewuduzaideMacBook-Pro:html runing_liu$ 
shuaiqidewuduzaideMacBook-Pro:html runing_liu$ pwd
/Users/runing_liu/Downloads/install-soft/nginx-1.16.0/html
```

	这也是上面在浏览器中输入 http://localhost，能够显示欢迎页面的原因。实际上访问的是 ../nginx-1.16.0/html 文件。

### 4.2 location 匹配规则

	语法规则:
 
	location [=|~|~*|^~] /uri/ { … } 

|模式|含义|
|-|-|-|
| location = /url   |=表示精确匹配 只有完全匹配才能生效|
| location ^~ /url  |^~ 开头对URL路径进行前缀匹配 并且在正则之前|
| llocation ~ pattern   |开头表示区分大小写的正则匹配|
| location ~* pattern   |开头表示不区分大小写的正则匹配|
| location /uri   |不带任何修饰符，也表示前缀匹配，但是在正则匹配之后|
| location /   |通用匹配，任何未匹配到其它location的请求都会匹配到，相当于switch中的default|

	前缀匹配时，Nginx 不对 url 做编码，因此请求为 /static/20%/aa，可以被规则 ^~ /static/ /aa 匹配到（注意是空格）

	多个 location 配置的情况下匹配顺序为（参考资料而来，还未实际验证，试试就知道了，不必拘泥，仅供参考）:

- 	首先精确匹配 =
- 	其次前缀匹配 ^~
- 	其次是按文件中顺序的正则匹配
- 	然后匹配不带任何修饰的前缀匹配。
- 	最后是交给 / 通用匹配
- 	当有匹配成功时候，停止匹配，按当前匹配规则处理请求


	实际使用中，应该至少有三个匹配规则定义，如下:

```language
# 直接匹配网站根，通过域名访问网站首页比较频繁，使用这个会加速处理，官网如是说。
# 这里是直接转发给后端应用服务器了，也可以是一个静态首页
# 第一个必选规则
location = / {
    proxy_pass http://tomcat:8080/index
}

# 第二个必选规则是处理静态文件请求，这是 nginx 作为 http 服务器的强项
# 有两种配置模式，目录匹配或后缀匹配，任选其一或搭配使用
location ^~ /static/ {
    root /webroot/static/;
}
location ~* \.(gif|jpg|jpeg|png|css|js|ico)$ {
    root /webroot/res/;
}

# 第三个规则就是通用规则，用来转发动态请求到后端应用服务器
# 非静态文件请求就默认是动态请求，自己根据实际把握
# 毕竟目前的一些框架的流行，带.php、.jsp后缀的情况很少了
location / {
    proxy_pass http://tomcat:8080/
}
# [更多详情=>原文链接地址](https://wiki.jikexueyuan.com/project/openresty/ngx/nginx_local_pcre.html)
```
	

	小编正在努力💪ing

《本站相关链接》:[(Nginx相关介绍(Nginx是什么?能干嘛?))](https://www.cnblogs.com/wcwnina/p/8728391.html)