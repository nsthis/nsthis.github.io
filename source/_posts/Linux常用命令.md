---
title: Linux常用命令
date: 2017-07-28 18:56:01
tags: Linux
categories: Linux

---

Linux下的常用命令

<!-- more -->

## 登陆服务器
```
	ssh username@ip -p (回车输密码)
```
## yum
yum主要负责安装一些所需要的linux命令
例如我们需要安装git:
```
	yum install -y git 
```
'-y'参数是'yes'的意思， 如果不加，那么在安装的时候会提示让你输入'yes'
## --help参数
linux里面每一个命令都会有一个help参数，它的作用是列举出你所想使用某一个命令的所有参数列表
## 创建一个用户
```
	useradd username;
	passwd password;
```
## 创建一个用户组
```
	groupadd groupname;
```
## 指定用户归属于哪个用户组
```
	useradd -g groupname username;
```
## 查看用户和用户组列表
```
	cat /etc/passwd; #可以查看所有用户的列表
	cat /etc/group; #查看用户组
```
## 删除用户和组列表
```
	userdel username; #删除用户
	userdel -r username; #删除用户同时删除用户的工作目录
	groupdel groupname; #删除用户组
	groupdel -r groupname; #删除用户组同时删除用户组的工作目录
```
## 修改用户组名
```
	groupmod -n groupname1 groupname2;
```
## 创建文件夹
```
	mkdir -p dirname;
```
## 删除文件
```
	rm dirname ;
	rm -rf dirname; #-rf 是不需要任何确认，直接删除
```
备注：千万不要使用 rm -rf /* ; 
## 变更文件或目录的权限
```
	chmod u+x dirname; #赋予文件的执行权限
	chmod 777 dirname; #赋予文件可读可写可执行权限
	chmod -R 777 dirname; #赋予文件及目录下的文件最高权限
```
## linux的权限说明
```
	w:4
	r:2
	x:1
	比如给一个用户读写权限，那么就是6
```
## 变更某个文件或目录的所有者和所属的组
```
	chown -R user dirname; #将dirname更换为user用户
	chown -R username:groupname dirname; #将dirname更换为user用户及groupname组
```
