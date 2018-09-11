---
title: docker命令 本地镜像管理
date: 2018-09-10 11:49:01
tags:
categories:
---
docker 本地镜像查看、删除、修改、build、历史、导出、导入。<!-- more -->
## docker images
列出本地镜像。
### 语法
```
docker images [OPTIONS] [REPOSITORY[:TAG]]
```
### OPTIONS说明：
```
-a :列出本地所有的镜像（含中间映像层，默认情况下，过滤掉中间映像层）；
--digests :显示镜像的摘要信息；
-f :显示满足条件的镜像；
--format :指定返回值的模板文件；
--no-trunc :显示完整的镜像信息；
-q :只显示镜像ID。
```
## docker rmi
删除本地一个或多少镜像。

### 语法
```
docker rmi [OPTIONS] IMAGE [IMAGE...]
```
### OPTIONS说明：
```
-f :强制删除；
--no-prune :不移除该镜像的过程镜像，默认移除；
```
## docker tag
标记本地镜像，将其归入某一仓库。
### 语法
```
docker tag [OPTIONS] IMAGE[:TAG] [REGISTRYHOST/][USERNAME/]NAME[:TAG]
```
## docker build
命令用于使用 Dockerfile 创建镜像。
### 语法
```
docker build [OPTIONS] PATH | URL | -
```
### OPTIONS说明：
```
--build-arg=[] :设置镜像创建时的变量；
--cpu-shares :设置 cpu 使用权重；
--cpu-period :限制 CPU CFS周期；
--cpu-quota :限制 CPU CFS配额；
--cpuset-cpus :指定使用的CPU id；
--cpuset-mems :指定使用的内存 id；
--disable-content-trust :忽略校验，默认开启；
-f :指定要使用的Dockerfile路径；
--force-rm :设置镜像过程中删除中间容器；
--isolation :使用容器隔离技术；
--label=[] :设置镜像使用的元数据；
-m :设置内存最大值；
--memory-swap :设置Swap的最大值为内存+swap，"-1"表示不限swap；
--no-cache :创建镜像的过程不使用缓存；
--pull :尝试去更新镜像的新版本；
--quiet, -q :安静模式，成功后只输出镜像 ID；
--rm :设置镜像成功后删除中间容器；
--shm-size :设置/dev/shm的大小，默认值是64M；
--ulimit :Ulimit配置。
--tag, -t: 镜像的名字及标签，通常 name:tag 或者 name 格式；可以在一次构建中为一个镜像设置多个标签。
--network: 默认 default。在构建期间设置RUN指令的网络模式
```
## docker history
查看指定镜像的创建历史。
### 语法
```
docker history [OPTIONS] IMAGE
```
### OPTIONS说明：
```
-H :以可读的格式打印镜像大小和日期，默认为true；
--no-trunc :显示完整的提交记录；
-q :仅列出提交记录ID。
```
## docker save
将指定镜像保存成 tar 归档文件。
### 语法
```
docker save [OPTIONS] IMAGE [IMAGE...]
```
### OPTIONS说明：
```
-o :输出到的文件。
```
## docker import
从归档文件中创建镜像。
### 语法
```
docker import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]
```
### OPTIONS说明：
```
-c :应用docker 指令创建镜像；
-m :提交时的说明文字；
```
