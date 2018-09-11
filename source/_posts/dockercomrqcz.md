---
title: docker命令 容器操作
date: 2018-09-10 11:48:14
tags:
  - docker
categories: linux
---
Docker 容器容器操作 查看、获取 。 <!-- more -->
<!-- more -->
## docker ps
列出容器
### 语法
```
docker ps [OPTIONS]
```
### OPTIONS说明：
```
-a :显示所有的容器，包括未运行的。
-f :根据条件过滤显示的内容。
--format :指定返回值的模板文件。
-l :显示最近创建的容器。
-n :列出最近创建的n个容器。
--no-trunc :不截断输出。
-q :静默模式，只显示容器编号。
-s :显示总的文件大小。
```
## docker inspect :
获取容器/镜像的元数据。
### 语法
```
docker inspect [OPTIONS] NAME|ID [NAME|ID...]
```
### OPTIONS说明：
```
-f :指定返回值的模板文件。
-s :显示总的文件大小。
--type :为指定类型返回JSON。
```
## docker top
查看容器中运行的进程信息，支持 ps 命令参数。
### 语法
```
docker top [OPTIONS] CONTAINER [ps OPTIONS]
```
容器运行时不一定有/bin/bash终端来交互执行top命令，而且容器还不一定有top命令，可以使用docker top来实现查看container中正在运行的进程。
## docker attach
连接到正在运行中的容器。
### 语法
```
docker attach [OPTIONS] CONTAINER
```
要attach上去的容器必须正在运行，可以同时连接上同一个container来共享屏幕（与screen命令的attach类似）。
官方文档中说attach后可以通过CTRL-C来detach，但实际上经过我的测试，如果container当前在运行bash，CTRL-C自然是当前行的输入，没有退出；如果container当前正在前台运行进程，如输出nginx的access.log日志，CTRL-C不仅会导致退出容器，而且还stop了。这不是我们想要的，detach的意思按理应该是脱离容器终端，但容器依然运行。好在attach是可以带上--sig-proxy=false来确保CTRL-D或CTRL-C不会关闭容器。

## docker events
从服务器获取实时事件

###语法
```
docker events [OPTIONS]
```
### OPTIONS说明：
```
-f ：根据条件过滤事件；
--since ：从指定的时间戳后显示所有事件;
--until ：流水时间显示到指定的时间为止；
```
## docker logs
获取容器的日志

### 语法
```
docker logs [OPTIONS] CONTAINER
```
### OPTIONS说明：
```
-f : 跟踪日志输出
--since :显示某个开始时间的所有日志
-t : 显示时间戳
--tail :仅列出最新N条容器日志
```
## docker wait
阻塞运行直到容器停止，然后打印出它的退出代码。

### 语法
```
docker wait [OPTIONS] CONTAINER [CONTAINER...]
```
## docker export
将文件系统作为一个tar归档文件导出到STDOUT。

### 语法
```
docker export [OPTIONS] CONTAINER
```
### OPTIONS说明：
```
-o :将输入内容写到文件。
```
## docker port
列出指定的容器的端口映射，或者查找将PRIVATE_PORT NAT到面向公众的端口。
### 语法
```
docker port [OPTIONS] CONTAINER [PRIVATE_PORT[/PROTO]]
```
