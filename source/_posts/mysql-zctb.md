---
title: mysql-server部署
date: 2017-08-02 19:48:30
tags:
- mysql
- 架构
categories: 数据库
---
MySQL 5.7是世界上最受欢迎的开源数据库的最佳版本，并提供了一个新的高级功能集，旨在帮助那些正在构建下一代基于Web和嵌入式应用程序和服务的人员。
<!-- more -->
## MySQL 5.7 特性
3倍更快的性能
新的优化器
本地JSON支持
多源复制
GIS空间扩展
和其他重要的增强
## mysql 添加apt源
```
wget https://repo.mysql.com//mysql-apt-config_0.8.7-1_all.deb
```
## 安装apt源
选择5.7版本
```
dpkg -i mysql-apt-config_0.8.7-1_all.deb
```
## 更新本地源
```
apt-get updagte
```
## 安装mysql
```
apt-get install mysql-server
```
