---
title: Docker搭建PHP开发环境
date: 2018-11-13 20:18:37
tags: Docker
categories: Docker
---
新手如何通过Docker搭建PHP开发环境，而不再是直接基于宿主机搭建
<!-- more -->

##	准备

如果使用阿里服务器那么你需要先登录阿里后台，安全配置将容器所用的端口打开，阿里不支持systemctl iport打开端口
mysql(3306)、php(9000)、nginx(80)

##	更新yum

yum -y update
//docker加速
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://68abbefd.m.daocloud.io  service docker restart

##	创建搭建目录

```
mkdir -p /mydata
vim dir.init
mkdir -p /mydata/deploy/nginx && \
mkdir -p /mydata/deploy/nginx/conf.d && \
mkdir -p /mydata/deploy/nginx/sites && \
mkdir -p /mydata/deploy/mysql && \
mkdir -p /mydata/deploy/php/sites 
:wq
chmod u+x dir.init 
./dir.init
```
##	搭建Docker环境(不推荐使用DockerFile)

```
cd /mydata;
vim docker.run;
sudo yum remove docker \
                  docker-common \
                  docker-selinux \
                  docker-engine \
&& \
sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2 \
&& \
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo \
&& \
sudo yum install docker-ce \
&& \
sudo systemctl start docker
:wq
chmod u+x docker.run
./docker.run
```

##	创建公用网络

```
docker network create demo
```
早期的方式一般用link还有就是简单或临时容器之间访问，如果说容器较多的时候用network，而且network可以动态设置不需要容器杀死。

##	安装Nginx

###	安装
```
docker search nginx //从docker库查看你想安装的nginx版本或者运行下面的命令直接拉取nginx最新的镜像
docker pull nginx //不指定版本号默认为latest
cd /mydata/deploy/nginx
vim docker.run
#docker stop nginx && docker rm nginx && \ //如果安装出错再打开
docker run \
--name nginx \
-p 80:80 \
-v `pwd`/sites:/usr/share/nginx/html:ro \
-v `pwd`/conf.d:/usr/share/nginx/conf.d \
-v `pwd`/conf.d:/etc/nginx/conf.d:ro \
--network demo \
-d \
nginx
:wq
chmod u+x docker.run
./docker.run
```
###	排错
1、输入服务器ip提示无权限访问
检测80端口是否开放
```
systemctl status firewalld //查看防火墙状态，
systemctl start firewalld //开启防火墙
firewall-cmd --zone=public --list-ports //查看开放的端口 是否有80 
firewall-cmd --zone=public --add-port=80/tcp --permanent //开放80端口
firewall-cmd --reload //使配置生效
```
2、iptables: No chain/target/match by that name.

在开发环境中，如果你删除了iptables中的docker链，或者iptables的规则被丢失了（例如重启firewalld），docker就会报iptables error例如：failed programming external connectivity ⋯ iptables: No chain/target/match by that name
```
systemctl restart docker //重启docker服务 再重复 ./docker.run 就ok了
!!!如果遇见其他错误自行google
vim /mydata/deploy/nginx/conf.d/test.localhost.com.conf 
server {
        server_name test.localhost.com;
        location / {
                root /usr/share/nginx/html;
        }
}
:wq
cd /mydata/deploy/nginx
./docker.run //每次修改配置你都需要重新生成容器,记得把第一行打开
浏览器运行，如果没什么问题 会访问nginx/sites/index.html
```
##	安装php

```
docker search php //我用的是php7.2
docker pull php:7.2-fpm
cd /mydata/deploy/php7
vim docker.run
#docker stop php7 && docker rm php7 \
docker run \
--restart=always \
-v $PWD/sites:/var/www/html \
-v $PWD/conf.d/sites-enabled:/usr/local/etc/php \
-p 9000:9000 \
--name=php7 \
--network demo \
-d \
php:7.2-fpm
:wq
chmod u+x docker.run
./docker.run
```
##	创建默认的nginx.conf

```
server {

  listen  80 default_server;

  server_name _;

  root   /usr/share/nginx/html;

  location / {

   index index.html index.htm index.php;

   autoindex off;

  }

  location ~ \.php(.*)$ {

   root   /var/www/html;

   fastcgi_pass php7:9000;   #因为我们最开始创建一个网络，所以这里可以直接用容器的名字

   fastcgi_index index.php;

   fastcgi_split_path_info ^((?U).+\.php)(/?.+)$;

   fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

   fastcgi_param PATH_INFO $fastcgi_path_info;

   fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;

   include  fastcgi_params;

  }

}
:wq
cd /mydata/deploy/nginx
./docker.run
vim /mydata/deploy/nginx/sites/index.php
<?php
echo phpinfo();
chmod 755 /mydata/deploy/nginx/sites/index.php
chmod 755 /mydata/deploy/nginx/conf.d/default.conf
此时访问服务器ip/index.php 应该会出现phpinfo的信息
```

##	安装MySQL

```
cd /mydata/deploy/mysql
vim docker.run
#docker stop mysql && docker rm mysql && \
docker run \
--name mysql \
-p 3306:3306 \
-v /mydata/deploy/mysql/dbdata:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=demo123 \
--network mall-net \
-d \
mysql
:wq
chmod u+x docker.run
docker pull mysql
./docker.run
```
###	允许外网访问
```
docker exec -it  mysql bin/bash
mysql -u root -p
use mysql;
grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option; 
flush privileges;
```
不出意外这时候你的数据库也可以访问了

















