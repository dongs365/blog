---
title: mariadb
date: 2019-09-06 11:27:57
tags:
- mysql
- 架构
categories: 数据库
---
MariaDB数据库管理系统是MySQL的一个分支，主要由开源社区在维护，采用GPL授权许可 MariaDB的目的是完全兼容MySQL，包括API和命令行，使之能轻松成为MySQL的代替品。
<!-- more -->

## MariaDB 数据库安装

### 安装源
```
curl -sS https://downloads.mariadb.com/MariaDB/mariadb_repo_setup | sudo bash
```
### 安装常用版本
服务器 客户端 集群
```
apt-get install mariadb-server galera-4 mariadb-client libmariadb3 mariadb-backup mariadb-common
```
### 安装【集群】
```
apt-get install mariadb-server mariadb-client galera-4
```
### 安装【客户端】
```
apt-get install mariadb-client libmariadb3
```
### 安装特定版本
```
apt-cache showpkg mariadb-server
apt-get install mariadb-server = 10.3.14-1 mariadb-client = 10.3.14-1 libmariadb3 = 10.3.14-1 mariadb-backup = 10.3.14-1 mariadb-common = 10.3.14-1
```
## 集群部署
### 配置每个节点都添加上
三节点演示
```
vim /etc/my.cnf
```
```
[galera]
wsrep_on=ON
wsrep_provider=/usr/lib/galera/libgalera_smm.so
wsrep_cluster_address="gcomm://192.168.207.100,192.168.207.101.192.168.207.102"
binlog_format=row
default_storage_engine=InnoDB
innodb_autoinc_lock_mode=2
bind-address=0.0.0.0
#wsrep_slave_threads=1
innodb_flush_log_at_trx_commit=0
#集群名称
wsrep_cluster_name = MyCluster
#当前主机信息
wsrep_node_name = T1
wsrep_node_address = "192.168.207.100"
```
### 关闭服务
```
systemctl start mariadb
```
### 创建集群
T1上操作
```
galera_new_cluster
```
### 防火墙需要开启的端口
```
3306（标准MariaDB端口）
4444（SST端口）
4567（Galera复制端口）
4568（IST端口）
```
### apparmor配置
```
cd /etc/apparmor.d/disable/
sudo ln -s /etc/apparmor.d/usr.sbin.mysqld
sudo systemctl restart apparmor
```

### 检查cluster大小：
```
show status like 'wsrep_cluster_size';
```
### 检查是否已同步数据修改
```
show status like 'wsrep_local_state_comment';
```
### 查看其他Galera状态
```
show status like 'wsrep%';
```
```
wsrep_cluster_size：集群节点数量；
wsrep_ready：服务状态；
wsrep_incoming_addresses：本节点域名信息。
```
### 清理节点
```
停止数据库服务器，删除配置，启动数据库
```
### 加入新节点
```
停止数据库服务器，新节点配置ok-添加ip到配文件，启动数据库
```