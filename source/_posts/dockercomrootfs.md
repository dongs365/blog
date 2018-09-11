---
title: docker命令 rootfs命
date: 2018-09-10 11:48:37
tags:
  - docker
categories: linux
---
docker 容器转镜像、拷贝文件、查看容器文件结构。<!-- more -->
## docker commit
从容器创建一个新的镜像。
### 语法
```
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
```
### OPTIONS说明：
```
-a :提交的镜像作者；
-c :使用Dockerfile指令来创建镜像；
-m :提交时的说明文字；
-p :在commit时，将容器暂停。
```
## docker cp
用于容器与主机之间的数据拷贝。
### 语法
```
docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH
```   
### OPTIONS说明：
```
-L :保持源目标中的链接
```
## docker diff
检查容器里文件结构的更改。
### 语法
```
docker diff [OPTIONS] CONTAINER
```
