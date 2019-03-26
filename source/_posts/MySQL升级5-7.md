---
title: MySQL升级5.7
date: 2019-03-14 10:53:51
tags: MySQL
categories:	MySQL

---
MySQl升级5.7的坑
<!--more-->

##  Group报错

### 报错信息

Expression of SELECT list is not in GROUP BY clause and contains nonaggregated column this is incompatible with sql_mode=only_full_group_by

### Yii2.0解决方案

````
common/config/main-local.php配置
'on afterOpen' => function($event) {
                $event->sender->createCommand("SET SESSION sql_mode = 'STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';")->execute();
            }
````

### 一次性解决

修改数据库配置

````
登陆数据库
mysql> SET GLOBAL sql_mode = '';
mysql> flush privileges;
````

##  权限分配

1、给某个人分配某个库的全部权限
````
 grant all privileges on databasename.* to 'username'@'host' identified by '1234';
````

##  MySQL当你只有.frm文件时

1、创建相同的数据库名
2、创建一个相同的表明，该表可以只是一个自增id
3、登陆服务器将之前相同表名的.frm文件进行替换
4、重启MySQL