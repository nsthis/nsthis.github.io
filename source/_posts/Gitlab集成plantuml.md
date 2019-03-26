---
title: Gitlab集成plantuml
date: 2019-03-26 09:35:23
tags: Docker
categories: Docker
---

因公司业务需要，要求GitLab集成UML

<!-- more -->

## UML

借用维基百科的一段解释：

````
统一建模语言（英语：Unified Modeling Language，缩写 UML）是非专利的第三代建模和规约语言。UML是一种开放的方法，用于说明、可视化、构建和编写一个正在开发的、面向对象的、软件密集系统的制品的开放方法。UML展现了一系列最佳工程实践，这些最佳实践在对大规模，复杂系统进行建模方面，特别是在软件架构层次已经被验证有效。

这个语言由葛来迪·布区，伊瓦尔·雅各布森与詹姆士·兰宝于1994年至1995年间，在Rational Software公司中开发，于1996年，又进一步发展。UML集成了Booch，OMT和面向对象软件工程的概念，将这些方法融合为单一的，通用的，并且可以广泛使用的建模语言。UML打算成为可以对并发和分布式系统的标准建模语言。

UML并不是一个工业标准，但在Object Management Group的主持和资助下，UML正在逐渐成为工业标准。OMG之前曾经呼吁业界向其提供有关面向对象的理论及实现的方法，以便制作一个严谨的软件建模语言（Software Modeling Language）。有很多业界的领袖亦真诚地回应OMG，帮助它建立一个业界标准。
````

## 安装plantuml

1、docker安装

````
docker run \
-d \
--restart=always \
--name plantuml \
-p 8080:8080 \
--network demo\
plantuml/plantuml-server:latest
````

2、Debian/Ubuntu

您需要plantuml.war从源代码创建一个文件：

````
sudo apt-get install graphviz openjdk-7-jdk git-core maven
git clone https://github.com/plantuml/plantuml-server.git
cd plantuml-server
mvn package
````
上面的命令序列将生成一个可以使用Tomcat部署的WAR文件：

````
sudo apt-get install tomcat7
sudo cp target/plantuml.war /var/lib/tomcat7/webapps/plantuml.war
sudo chown tomcat7:tomcat7 /var/lib/tomcat7/webapps/plantuml.war
sudo service tomcat7 restart
````

3、运行

plantuml监听的是8080端口 即http://localhost:8080

## Gitlab配置plantuml

````
1、因为涉及配置问题，所以你的gitlab需要用管理员账号登陆
2、点击Admin Area
3、找到左边菜单栏的Settings
4、点击Integrations就可以看到PlantUML的配置项
5、里面填写你的http://localhost:8080即可
````

## 注意事项

如果无法运行或者plantuml容器已经启动但是无法访问，请检查防火墙问题