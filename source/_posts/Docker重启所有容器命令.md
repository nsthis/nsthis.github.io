---
title: Docker重启所有容器命令
date: 2019-02-11 15:00:25
tags: Docker
categories: Docker
---
Docker重启所有命令以及构建是如何设置开机重启

<!-- more -->

##  设置开机重启

````
--restart=always 
````

## 启动所有容器

````
docker start $(docker ps -a | awk '{ print $1}' | tail -n +2)
````

##  关闭所有容器

````
docker stop $(docker ps -a | awk '{ print $1}' | tail -n +2)
````

##  删除所有容器

````
docker rm $(docker ps -a | awk '{ print $1}' | tail -n +2)
````

##  删除所有镜像

````
docker rmi $(docker p_w_picpaths | awk '{print $3}' |tail -n +2)
````

##  重启所有容器

````
docker restart $(docker ps -a | awk '{ print $1}' | tail -n +2)
````