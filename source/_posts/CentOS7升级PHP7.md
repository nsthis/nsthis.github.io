---
title: CentOS7升级PHP7
date: 2018-12-20 20:07:57
tags: PHP
categories: PHP
---

当我们使用Docker之后，很多Composer会要求PHP版本，而CentOS7系统本身自带版本为5.4.x版本，所以我们必须要进行升级

<!-- more -->

##  安装EPEL和Remi的软件源，执行如下命令安装：

````
cd /root
yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
````

##  安装yum-utils工具

````
yum install yum-utils
````

##  yum-utils的提供了一个yum-config-manager程序

以下命令选择其一，根据需要安装的版本
````
yum-config-manager --enable remi-php70   [安装 PHP 7.0]
yum-config-manager --enable remi-php71   [安装 PHP 7.1]
yum-config-manager --enable remi-php72   [安装 PHP 7.2]
````

##  将PHP及常的扩展安装至系统：

````
yum install php php-mcrypt php-cli php-gd php-curl php-mysql php-ldap php-zip php-fileinfo
````
