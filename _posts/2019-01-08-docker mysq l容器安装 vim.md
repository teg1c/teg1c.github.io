---
layout: mypost
title: docker mysql 容器安装 vim
categories: [DOCKER,MYSQL]
---

需求要改 mysql 的配置参数，才发现容器内并没有 vim 插件。

进入容器
----
宿主机执行`docker exec -it <mysql容器id> bash`


配置国内镜像源
-------
    mv /etc/apt/sources.list /etc/apt/sources.list.bak
    echo "deb http://mirrors.163.com/debian/ jessie main non-free contrib" >/etc/apt/sources.list
    echo "deb http://mirrors.163.com/debian/ jessie-proposed-updates main non-free contrib" >>/etc/apt/sources.list
    echo "deb-src http://mirrors.163.com/debian/ jessie main non-free contrib" >>/etc/apt/sources.list
    echo "deb-src http://mirrors.163.com/debian/ jessie-proposed-updates main non-free contrib" >>/etc/apt/sources.list
    #更新安装源
    apt-get update 


安装vim
-----
    #安装命令
    apt-get install -y vim