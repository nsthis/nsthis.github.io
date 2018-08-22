---
title: Yii2.0
date: 2018-05-25 18:05:56
tags: PHP框架
categories: PHP框架
---
Yii框架对面向对象的要求比较高，所以需要我们有一定的OOP思想。

<!-- more -->

##	官网
https://www.yiiframework.com/

##	安装

1、通过Composer安装
2、通过下载一个所需文件以及Yii框架文件的应用模板

###	通过Composer安装

```
composer global require "fxp/composer-asset-plugin:^1.2.0"
composer create-project --prefer-dist yiisoft/yii2-app-basic basic
composer create-project yiisoft/yii2-app-advanced advanced
```

###	通过归档文件安装

直接在官网下载即可

##	配置
composer下载的yii框架是自带密钥的，但如果是从官网下载的，则需要进行如下操作

```
// !!! 在下面插入一段密钥（若为空） - 以供 cookie validation 的需要
'cookieValidationKey' => '在此处输入你的密钥',
```
备注：如果你不添加密钥，则运行的时候会直接报错

##	文件目录
yii框架有2个版本，到时候具体看你选择

###	基础版：
basic 目录下的各个文件夹：
commands            控制台
config                    配置文件
console.php          控制台配置
db.php                   数据库连接配置
params.php           项目中的变量配置
web.php                web应用配置
controllers            控制器层
models                 模型层
runtime                运行时的临时文件目录（缓存文件和日志文件），需要写权限
vendor                  存放 Yii 的源码和通过 composer 安装的项目依赖库
views                     视图层
web                       域名指向的目录，包含入口文件，可存放css、js、img、font等静态资源

###	高级版：
advanced 			 目录下的各个文件夹：
backend                后台应用
common                公共目录
console                  控制台应用
environments        环境目录（开发环境和生产环境）
frontend                前台应用
vendor                   存放 Yii 的源码和通过 composer 安装的项目依赖库


