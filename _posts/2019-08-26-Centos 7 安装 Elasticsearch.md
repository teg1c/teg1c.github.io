---
layout: mypost
title: Centos 7 安装 Elasticsearch
categories: [CENTOS,WINDOWS,elasticsearch]
---

Elasticsearch 版本：7.2

Elasticsearch 从 7.0 开始内置了 Java 环境，无需单独安装JDK

# 安装与配置

> 官方文档：https://www.elastic.co/guide/en/elasticsearch/reference/current/getting-started-install.html

下载
```angular2
curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.2.0-linux-x86_64.tar.gz
```

解压

```angular2
tar -xvf elasticsearch-7.2.0-linux-x86_64.tar.gz
```

安装目录 `/data/elasticsearch`

```angular2
mkdir -p /data/elasticsearch
```
移动所有文件至 `/data/elasticsearch`

```angular2
mv ./elasticsearch-7.2.0/* /data/elasticsearch
```
创建普通用户 `es`

```angular2
# 以root用户来创建新的用户 ， groupadd 添加一个用户组
groupadd es

# 添加一个用户，-g是在用户组下 -p是密码
useradd es -g es -p es
 
# 给用户 es 授权
chown -R es:es /data/elasticsearch
```

后续所有操作都在 `/data/elasticsearch` 目录里面进行

```angular2
cd /data/elasticsearch
su es
```
编辑 `./config/elasticsearch.yml`

```angular2
# 添加或修改
node.name: node-1
network.host: 0.0.0.0
http.port: 9200
cluster.initial_master_nodes: ["node-1"]
```
修改系统配置，编辑 `/etc/sysctl.conf`

```angular2
# 添加或修改
# sudo sysctl -p 使修改生效
vm.max_map_count = 262144
```

修改 ECS 安全组，放行 `9200` 端口

启动 `elasticsearch`

```angular2
./bin/elasticsearch -d
```