---
title: Docker
date: 2017-10-15 20:04:29
tags: Docker
categories: Docker
---
初识Docker是我在公司于公司其他部们负责人聊天的时候，知道公司的服务器使用Docker来搭建的，本着好奇，所以折腾折腾

<!-- more -->

## 	Docker的介绍
Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口

##	为什么要用Docker
作为一种轻量级的虚拟化方式，Docker在运行应用上跟传统的虚拟机方式相比具有显著优势：
1、Docker容器很快，启动和停止可以在秒级实现，这相比传统的虚拟机方式要快得多。
2、Docker容器对系统资源需求很少，一台主机上可以同时运行数千个Docker容器。
3、Docker通过类似Git的操作来方便用户获取、分发和更新应用镜像，指令简明，学习成本较低。
4、Docker通过Dockerfile配置文件来支持灵活的自动化创建和部署机制，提高工作效率。

Docker容器除了运行其中的应用之外，基本不消耗额外的系统资源，保证应用性能的同时，尽量减小系统开销。传统虚拟机方式运行N个不同的应用就要启动N个虚拟机（每个虚拟机需要单独分配独占的内存、磁盘等资源），而Docker只需要启动N个隔离的容器，并将应用放到容器内即可。

##	Docker的好处
1、更快速的交付和部署。使用Docker，开发人员可以使用镜像来快速构建一套标准的开发环境；开发完成之后，测试和运维人员可以直接使用相同环境来部署代码。Docker可以快速创建和删除容器，实现快速迭代，大量节约开发、测试、部署的时间。并且，各个步骤都有明确的配置和操作，整个过程全程可见，使团队更容易理解应用的创建和工作过程。
2、更高效的资源利用。Docker容器的运行不需要额外的虚拟化管理程序（Virtual Machine Manager，VMM，以及Hypervisor）支持，它是内核级的虚拟化，可以实现更高的性能，同时对资源的额外需求很低。
3、更轻松的迁移和扩展。Docker容器几乎可以在任意的平台上运行，包括物理机、虚拟机、公有云、私有云、个人电脑、服务器等。 这种兼容性让用户可以在不同平台之间轻松地迁移应用。
4、更简单的更新管理。使用Dockerfile，只需要小小的配置修改，就可以替代以往大量的更新工作。并且所有修改都以增量的方式进行分发和更新，从而实现自动化并且高效的容器管理。

## 	Docker命令

1、docker images;		#查看本地镜像
2、docker ps;		#查看本机正在运行的容器
3、docker ps -a;		#查看本机所有的容器
4、docker run;		#运行容器
5、docker exec	-it 容器名称 bin/bash;		#进入容器
6、docker start $(docker ps -a | awk '{ print $1}' | tail -n +2);		#启动所有的容器命令
7、docker restart $(docker ps -a | awk '{ print $1}' | tail -n +2);		#重启所有的容器命令
8、docker stop $(docker ps -a | awk '{ print $1}' | tail -n +2);		#关闭所有容器
9、docker rm $(docker ps -a | awk '{ print $1}' | tail -n +2);			#删除所有的容器命令
10、docker rmi $(docker images | awk '{print $3}' |tail -n +2);		#删除所有的镜像
11、docker logs 容器名称或者容器id:;		#查看容器日志
12、docker serach 镜像名称;		#查看docker远程仓库的镜像版本
13、docker pull 镜像版本;		#可以从docker远程仓库拉取该镜像
14、docker login;		#登陆hub.docker


