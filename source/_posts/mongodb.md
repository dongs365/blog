---
title: mongodb
date: 2017-08-02 16:31:59
tags:
- mongodb
categories: 数据库
---
MongoDB是一个开源文档数据库，可提供高性能，高可用性和自动缩放。
<!-- more -->
## MongoDB社区版简介
MongoDB中的一个记录是一个文档，它是一个由字段和值对组成的数据结构。MongoDB文档类似于JSON对象。字段的值可能包括其他文档，数组和文档数组。
### 特点
高性能
丰富查询语言
高可用性
水平可扩展性
支持多种存储引擎
### 包内容
MongoDB在自己的存储库中提供正式支持的软件包。此存储库包含以下软件包：

| 包名称 | 描述 |
|:-----|:-----|
| mongodb-org | 将自动安装以下列出的四个组件包 |
| mongodb-org-server | 包含mongod守护程序和关联的配置和init脚本。|
| mongodb-org-mongos | 包含mongos守护进程。 |
| mongodb-org-shell | 包含mongoshell。 |
| mongodb-org-tools | 包含以下的MongoDB工具 mongoimport bsondump, mongodump, mongoexport, mongofiles, mongooplog, mongoperf, mongorestore, mongostat, and mongotop |

## 安装MongoDB社区版
### Redhat
#### 配置YUM源
创建一个/etc/yum.repos.d/mongodb-org-3.4.repo文件，使您可以直接安装MongoDB yum。
```
[mongodb-org-3.4]
name = MongoDB Repository
baseurl = https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
gpgcheck = 1
enabled = 1
gpgkey = https：// www.mongodb.org/static/pgp/server-3.4.asc
```
#### 安装MongoDB包
```
yum install -y mongodb-org
```

### Ubuntu
#### 导入包APT管理的公钥
```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
```
#### 为MongoDB创建一个安装源文件
/etc/apt/sources.list.d/mongodb-org-3.4.list使用适合您的Ubuntu版本的命令创建列表文件：
Ubuntu 12.04
```
echo "deb [ arch=amd64 ] http://repo.mongodb.org/apt/ubuntu precise/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
```
Ubuntu 14.04
```
echo "deb [ arch=amd64 ] http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
```
Ubuntu 16.04
```
echo "deb [ arch=amd64,arm64 ] http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
```

#### 重新加载本地数据源。
```
sudo apt-get update
```

#### 安装MongoDB包
```
sudo apt-get install -y mongodb-org
```

## 运行MongoDB社区版
### 启动MongoDB
```
service mongod start
```
### 验证MongoDB是否成功启动
mongod通过检查日志文件的内容进行/var/log/mongodb/mongod.log 读取，验证进程是否成功启动
```
[initandlisten] waiting for connections on port <port>
```
其中<port>被配置为在该端口/etc/mongod.conf，27017默认端口
### 停止MongoDB
```
service mongod stop
```
### 重新启动MongoDB
```
sudo service mongod restart
```
### 开始使用MongoDB

## 卸载MongoDB社区版

###停止MongoDB
```
sudo service mongod stop
```
### 删除软件包
#### ubuntu
```
sudo apt-get purge mongodb-org *
```
#### redhat
```
sudo yum erase $(rpm -qa | grep mongodb-org)
```
### 删除数据目录
```
sudo rm -r / var / log / mongodb
sudo rm -r / var / lib / mongodb
```
