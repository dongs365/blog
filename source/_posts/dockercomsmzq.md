---
title: docker命令 生命周期
date: 2018-09-10 11:19:27
tags:
  - docker
categories: docker
---
Docker 容器生命周期管理 创建、启动、停止、重启、结束、暂停、恢复、创建、执行 。 <!-- more -->

## docker run
创建一个新的容器并运行一个命令
```
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```
### OPTIONS说明：
```
-a stdin: 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；
-d: 后台运行容器，并返回容器ID；
-i: 以交互模式运行容器，通常与 -t 同时使用；
-p: 端口映射，格式为：主机(宿主)端口:容器端口
-t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用；
--name="nginx-lb": 为容器指定一个名称；
--dns 8.8.8.8: 指定容器使用的DNS服务器，默认和宿主一致；
--dns-search example.com: 指定容器DNS搜索域名，默认和宿主一致；
-h "mars": 指定容器的hostname；
-e username="ritchie": 设置环境变量；
--env-file=[]: 从指定文件读入环境变量；
--cpuset="0-2" or --cpuset="0,1,2": 绑定容器到指定CPU运行；
-m :设置容器使用内存最大值；
--net="bridge": 指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型；
--link=[]: 添加链接到另一个容器；
--expose=[]: 开放一个端口或一组端口；
```
## Docker start/stop/restart
### docker start :启动一个或多少已经被停止的容器
### docker stop :停止一个运行中的容器
### docker restart :重启容器

### 语法
```
docker start [OPTIONS] CONTAINER [CONTAINER...]
docker stop [OPTIONS] CONTAINER [CONTAINER...]
docker restart [OPTIONS] CONTAINER [CONTAINER...]
```
## docker kill
杀掉一个运行中的容器。
### 语法
```
docker kill [OPTIONS] CONTAINER [CONTAINER...]
```
### OPTIONS说明：
```
-s :向容器发送一个信号
```
## docker rm
删除一个或多少容器
### 语法
```
docker rm [OPTIONS] CONTAINER [CONTAINER...]
```
### OPTIONS说明：
```
-f :通过SIGKILL信号强制删除一个运行中的容器
-l :移除容器间的网络连接，而非容器本身
-v :-v 删除与容器关联的卷
```
## docker pause
暂停容器中所有的进程。
## docker unpause
恢复容器中所有的进程。
### 语法
```
docker pause [OPTIONS] CONTAINER [CONTAINER...]
docker unpause [OPTIONS] CONTAINER [CONTAINER...]
```
## docker create
创建一个新的容器但不启动它
语法同 docker run
### 语法
```
docker create [OPTIONS] IMAGE [COMMAND] [ARG...]
```
## docker exec
在运行的容器中执行命令
### 语法
```
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```
### OPTIONS说明：
```
-d :分离模式: 在后台运行
-i :即使没有附加也保持STDIN 打开
-t :分配一个伪终端
```
