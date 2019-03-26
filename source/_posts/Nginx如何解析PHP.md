---
title: Nginx如何解析PHP
date: 2018-08-10 17:10:18
tags: Nginx
categories: Nginx
---

在我们的代码开发中，你是否真正去了解过Nginx如何解析PHP的?

<!-- more --> 
##	环境LNMP

我之前讲到过如何搭建PHP的运行环境，那么我们如何配置Nginx？

##	安装PHP-fpm

你(PHP)去和爱斯基摩人(web服务器，如 Apache、Nginx)谈生意
你说中文(PHP代码)，他说爱斯基摩语(C代码)，互相听不懂，怎么办？那就都把各自说的话转换成英语(FastCGI 协议)吧。
怎么转换呢？你就要使用一个翻译机(PHP-FPM)
(当然对方也有一个翻译机，那个是他自带的)
我们这个翻译机是最新型的，老式的那个（PHP-CGI）被淘汰了。不过它(PHP-FPM)只有年轻人（Linux系统）会用，老头子们（Windows系统）不会摆弄它，只好继续用老式的那个。

备注：该解释来自:https://segmentfault.com/q/1010000000256516

```
yum install php-fpm
service php-fpm start    #启动 php-fpm
```

##	修改nginx配置文件的nginx.conf识别php

```
vim nginx.conf的路径
location ~ \.php$ {
	root           html;	#php的文件路径
   	fastcgi_pass   127.0.0.1:9000;
  	fastcgi_index  index.php;
   	fastcgi_param SCRIPT_FILENAME /usr/share/nginx/html/$fastcgi_script_name;
   	include        fastcgi_params;
}
service nginx restart
service php-fpm restart
```
完成


