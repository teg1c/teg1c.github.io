---
layout: mypost
title: Docker Swarm 常用命令
categories: [Docker]
---

## 创建集群

```
docker swarm init --advertise-addr  192.168.2.123
```

## Node 节点加入集群

```
docker swarm join --token "创建集群的tocken值" 192.168.2.123:2377
#如果忘记了加入集群的命令，可以在管理节点执行以下命令获取
[root@manager123 ~]# >>>docker swarm join-token manager  #查看加入管理节点的tocken
To add a manager to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-5n0yqdoirvjroddv4tk4q352rjg5ywqo0gur2a5ibaw517y9ed-6bwuuocgw1lx3anqsiy373uyn 192.168.2.123:2377

[root@manager123 ~]# >>>docker swarm join-token  worker  #查看加入工作节点的tocken
To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-5n0yqdoirvjroddv4tk4q352rjg5ywqo0gur2a5ibaw517y9ed-5lo62wh31nrt439b679s2ulim 192.168.2.123:2377

```

## 查看集群中的服务

```
docker service ls 
docker service ls ps 服务名
```

## 查看服务部署在哪个节点

```
docker service ps 服务名
```

## 修改服务实例数

```
docker service scale nginx=3
```

## 删除节点

```angular2html
docker node rm node2
```

## 排除节点

```angular2html
docker node update --availability drain <NODE-ID>
```

## 恢复排除的节点

```angular2html
docker node update --availability active <NODE-ID>
```

## 删除 `shutdown` 的容器,和无用数据清理
```angular2html

docker  system prune -f
docker node常用命令
docker node ls      #查看所有集群节点
docker node rm      #删除某个节点（-f强制删除）
docker node inspect ##查看节点详情,标签   --pretty 显示信息比较规整
docker node demote  #节点降级，由管理节点降级为工作节点
docker node promote #节点升级，由工作节点升级为管理节点
# docker node promote node1 node2 # 提升node1 node2节点为管理节点
docker node update  #更新节点
docker node ps      #查看节点中的 Task 任务
```
## docker service 常用命令

```
docker service create    #创建服务, 如果编写compose文件，可以使用docker stack命令部署
docker service inspect   #查看服务的详细信息
docker service ps        #查看服务运行的节点
docker service logs  	   #查看某个服务的日志信息
docker service rm 		   #删除服务
docker service ls        #列出集群中所有服务
docker service ls --filter name=字符串匹配服务名  #过滤service
docker service update    #更新服务
docker service update --image  hub.com/image  service_name  #更新服务的镜像
```

## docker swarm服务的动态命令设置

```
# 命令格式 docker service [option] service
docker service update --env-add
docker service update --env-rm  
docker service update --host-add
docker service update --host-rm
docker service update --hostname
docker service update --mount-add type=volume,source=/data,target=/data
docker service update --mount-rm  type=volume,source=/data,target=/data
docker service update --network-add name=my-network,alias=web1   # Add a network
docker service update --network-rm  name=my-network,alias=web1
docker service update --publish-add published=8080,target=80 # Add or update a published port
docker service update --publish-rm  published=8080,target=80  # Remove a published port by its target port
```
## 批量删除所有服务

```
docker service ls -1  # 获取所有service的ID
docker service ls -q | xargs docker service rm
```
## Docker Stack 部署多个集群服务
`docker stack `使用文件  `docker-compose.yml `批量部署服务
创建编排文件 `docker-compose.yml`
```
version: '3'
services:

mynginx:
image: hub.test.com:5000/almi/nginx:0.1
ports:
- "8081:80"
deploy:
replicas: 3

busybox:
image: hub.test.com:5000/busybox:latest
volumes:
- "/var/run/docker.sock:/var/run/docker.sock"
deploy:
replicas: 2

```
## 使用docker-stack批量创建服务
```
docker stack deploy -c docker-compose.yml deploy-test
docker stack ps deploy-tes

# docker stack部署的容器名称 deploy-test_<service名称>.随机后缀
```

## docker stack 常用命令

```
docker stack deploy     #部署新的堆栈或更新现有堆栈
docker stack ls         #列出现有堆栈
docker stack ps         #列出堆栈中的任务
docker stack rm         #删除堆栈 （docker stack deploy部署的时候指定的服务名字）
docker stack services   #列出堆栈中的服务
docker stack down       #移除某个堆栈（不删数据）
docker stack deploy 显示服务的状态
accepted: 任务已经被分配到某一个节点执行
preparing: 准备资源，一般是从网络拉取iamge
running: 副本运行成功
shutdown: 报错，终止，当一个任务被终止（stoped or killed），任务不能被重启，但是一个替代的任务会被重启
```
## 查看swarm中服务的ip
```
docker service inspect --format '{{ .Endpoint.VirtualIPs }}'  服务名
```
