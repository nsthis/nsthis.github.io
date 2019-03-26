---
title: Docker的PHP环境开扩展
date: 2019-01-17 14:04:50
tags: Docker
categories: Docker
---

PHP官方镜像来run一个容器之后，PHP官方有一些我们常用的扩展是不开启的

<!-- more -->

##  前提

以下所有命令均需进入容器内运行

##  GD库

````
apt-get update
apt-get install libpng-dev libjpeg-dev libfreetype6-dev
docker-php-ext-install gd
````

### 问题
1、若遇到提示无gd.ini文件可进行下列操作

````
以下为宿主机操作：
1、找到php容器的/usr/local/etc/php挂在路径
2、vim conf.d/docker-php-ext-gd.ini
3、写入:extension=pdo_mysql.so

以下为容器内操作：
cd /usr/local/etc/php/conf.d
vim docker-php-ext-gd.ini
extension=gd.so
:wq
````
2、当gd库不支持jpeg格式时

````
docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/
docker-php-ext-install -j$(nproc) gd
````
## pdo_mysql

````
cd /usr/local/bin
docker-php-ext-install pdo_mysql
````

注：若遇到提示无pdo_mysql.ini文件可进行下列操作

````
以下为宿主机操作：
1、找到php容器的/usr/local/etc/php挂在路径
2、vim conf.d/docker-php-ext-pdo_msql.ini
3、写入:extension=pdo_mysql.so

以下为容器内操作：
cd /usr/local/etc/php/conf.d
vim docker-php-ext-pdo_msql.ini
extension=pdo_mysql.so
:wq
````

##  mysqli

````
docker-php-ext-install mysqli
````



