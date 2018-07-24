---
title: MySQL权限控制
date: 2017-07-25 18:07:56
tags: MySQL
categories: MySQL

---

如何操作数据库的权限，下面我会根据不同的场景配合命令进行说明
<!-- more --> 

## 数据库的权限
查找(SELECT)、 增加(INSERT)、修改(UPDATE)、删除(DELETE)
## 限制root登陆ip

一般数据库创建的时候，root登陆的ip为localhost， 但是现在更多的是使用工具(至少我身边的人),所以我们需要先把root账号设置成我们公司或者家庭的固定ip。那么如何查看自己的ip地址？打开度娘然后输入ip，就可以了。 
```
	msyql -h localhost -u -p (回车输密码)
	user mysql;
	grant all privileges on *.* to 'root'@'ip' identified by 'password'; 
	flush privileges;
```
备注： 如果命令执行完成， 但你使用工具无法连接时，那么建议你查看一下服务器的3306端口是否开启，如果是阿里的，那么登陆阿里的控制台，在安全组内开通。
如果是自己的服务器，那么再后续的文章中我会讲解。

## 某个用户拥有对某个数据库的权限
我们一般拥有很多的项目，那么就需要分配不同的用户去查找不同的数据库，那么这时候我们就需要以下命令:
```
	create user username@'ip' identified by 'password';
	grant all privileges on db.* to 'username'@'%' identified by 'password'; 
	grant create on db.* to 'username'@'%' identified by 'password'; 
	grant alter on db.* to 'username'@'%' identified by 'password'; 
	grant drop on db.* to 'username'@'%' identified by 'password'; 
	flush privileges;
```
备注：all可以更换为：查找(SELECT)、 增加(INSERT)、修改(UPDATE)、删除(DELETE)

## 修改某个用户的权限
比如你的一个同事，刚入职的时候，你给他的是某个数据库的SELECT权限，但是工作时间久了或者是因为特定原因，现在需要讲他的权限改为全部，那么你需要下面的命令
```
	evoke all privileges on db.* from 'username'@'ip';
	grant all privileges on db.* to 'username'@'%' identified by 'password'; 
	flush privileges;
```
备注：最好不要删除用户，因为你删除了用户，但却没有删除用户的权限，所以以后再添加一个用户的时候，可能会出现问题
## 删除一个用户及他的权限
如果你公司的某个员工离职了，那么你需要把他的权限给删除掉，当然一般都是设定了ip，不会发生这种情况
```
	delete from mysql.user  where user = "username";
	revoke insert,update,delete,select on *.* from 'db'@'ip' identified by 'password'; 
	flush privileges;
```
## 给用户lock
```
	alter user 'username'@'ip' account lock; 增加lock
	alter user 'username'@'ip' account unlock; 删除lock
```
当客户端使用lock状态的用户登录MySQL时，会收到如此报错 Access denied for user 'username'@'ip'
## 查看用户的权限
```
	show grants for username;
```
## Grant的语法及权限列表
	1、 语法
```
	GRANT privileges (columns) ON db.tables TO user IDENTIFIED BY "password" WITH GRANT OPTION;
```
	2、 权限列表
```
	ALTER: 修改表和索引。
	CREATE: 创建数据库和表。
	DELETE: 删除表中已有的记录。
	DROP: 抛弃(删除)数据库和表。
	INDEX: 创建或抛弃索引。
	INSERT: 向表中插入新行。
	REFERENCE: 未用。
	SELECT: 检索表中的记录。
	UPDATE: 修改现存表记录。
	FILE: 读或写服务器上的文件。
	PROCESS: 查看服务器中执行的线程信息或杀死线程。
	RELOAD: 重载授权表或清空日志、主机缓存或表缓存。
	SHUTDOWN: 关闭服务器。
	ALL: 所有权限，ALL PRIVILEGES同义词。
	USAGE: 特殊的 "无权限" 权限。
```



















