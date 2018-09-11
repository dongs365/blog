---
title: docker命令 镜像仓库
date: 2018-09-10 11:48:47
tags:
  - docker
categories: linux
---
docker 仓库登陆、登出、镜像拉取、推送、搜索。<!-- more -->
## docker login
登陆到一个Docker镜像仓库，如果未指定镜像仓库地址，默认为官方仓库 Docker Hub
## docker logout
登出一个Docker镜像仓库，如果未指定镜像仓库地址，默认为官方仓库 Docker Hub
### 语法
```
docker login [OPTIONS] [SERVER]
docker logout [OPTIONS] [SERVER]
```
### OPTIONS说明：
```
-u :登陆的用户名
-p :登陆的密码
```
## docker pull
从镜像仓库中拉取或者更新指定镜像

### 语法
```
docker pull [OPTIONS] NAME[:TAG|@DIGEST]
```
### OPTIONS说明：
```
-a :拉取所有 tagged 镜像
--disable-content-trust :忽略镜像的校验,默认开启
```
## docker push
将本地的镜像上传到镜像仓库,要先登陆到镜像仓库

### 语法
```
docker push [OPTIONS] NAME[:TAG]
```
### OPTIONS说明：
```
--disable-content-trust :忽略镜像的校验,默认开启
```
## docker search
从Docker Hub查找镜像
### 语法
```
docker search [OPTIONS] TERM
```
### OPTIONS说明：
```
--automated :只列出 automated build类型的镜像；
--no-trunc :显示完整的镜像描述；
-s :列出收藏数不小于指定值的镜像。
```
