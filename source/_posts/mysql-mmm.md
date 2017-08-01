---
title: mysql-mmm 部署指南
date: 2017-08-02 16:32:11
tags:
- mysql
- 架构
categories: 数据库
---
mysql-mmm多主复制管理器 是一组灵活的脚本（在任何时间只有一个节点可写）来执行监视/故障转移和MySQL主-主复制配置管理工具。
该工具集还能够通过任意数量的从站读取平衡标准主/从配置，因此您可以使用它来移动一组服务器上的虚拟IP地址，具体取决于它们是否在复制中。
<!-- more -->
## 部署环境准备
最基本的安装必须至少包含2个数据库服务器和一个监控服务器，这边的环境是5台主机
系统：ubuntu 14.04
MMM软件：2.2.1

|主机类型|ip地址|主机名称|服务 id | 备注
|:-------|:------|:-------|:-------|
|monitoring host|192.168.0.10|mon|-|监控
|master 1|192.168.0.11|      db1|1|主
|master 2|192.168.0.12|      db2|2|主
|slave  1|192.168.0.13|      db3|3|从
|slave  2|192.168.0.14|      db4|4|从

使用以下虚拟 IP。他们将通过MMM分布在主机上。

|ip地址|角色|描述
|:---|:---|:---|
|192.168.0.100|读写|连接到此IP以进行写入查询
|192.168.0.100|读|连接到这个IP进行读取查询
|192.168.0.100|读|连接到这个IP进行读取查询
|192.168.0.100|读|连接到这个IP进行读取查询
|192.168.0.100|读|连接到这个IP进行读取查询
## Mysql 数据库配置
db1 db2 双主相互同步     db3 db4 做db1的从库
### 设置主从同步
#### 锁表禁止写入
```
mysql> FLUSH TABLES WITH READ LOCK;
```
#### 显示db1的MASTER地址
```
mysql> SHOW MASTER STATUS;
```

| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
|------------------|----------|--------------|------------------|
| mysql-bin.000001 |      107 |              |                  |
|1 row in set (0.00 sec)|

#### 备份所有数据库(新的终端打开)
```
mysqldump -u root -p --all-databases > /tmp/database-backup.sql
```

#### 对数据库解除锁定
```
mysql> UNLOCK TABLES;
```
#### 数据库同步到其他
复制刚才的数据库备份到db2, db3 and db4：
```
db1$ scp /tmp/database-backup.sql @192.168.0.12:/tmp
db1$ scp /tmp/database-backup.sql @192.168.0.13:/tmp
db1$ scp /tmp/database-backup.sql @192.168.0.14:/tmp
```
然后将备份导入到db2, db3 and db4：
```
db2$ mysql -u root -p < /tmp/database-backup.sql
db3$ mysql -u root -p < /tmp/database-backup.sql
db4$ mysql -u root -p < /tmp/database-backup.sql
```
然后刷新db2, db3 and db4的权限，我们更改了user表，所以mysql要重新读取这个表
```
(db2) mysql> FLUSH PRIVILEGES;
(db3) mysql> FLUSH PRIVILEGES;
(db4) mysql> FLUSH PRIVILEGES;
```
#### 设置同步
通过下面的命令设置在db2, db3和db4上的复制
```
(db2) mysql> CHANGE MASTER TO master_host='192.168.0.11', master_port=3306, master_user='replication',
              master_password='replication_password', master_log_file='mysql-bin.000001', master_log_pos=107;
(db3) mysql> CHANGE MASTER TO master_host='192.168.0.11', master_port=3306, master_user='replication',
              master_password='replication_password', master_log_file='mysql-bin.000001', master_log_pos=107;
(db4) mysql> CHANGE MASTER TO master_host='192.168.0.11', master_port=3306, master_user='replication',
              master_password='replication_password', master_log_file='mysql-bin.000001', master_log_pos=107;
```
#### 启动从库slave同步
```
(db2) mysql> START SLAVE;
(db3) mysql> START SLAVE;
(db4) mysql> START SLAVE;
```

#### 检查同步情况
```
(db2) mysql> SHOW SLAVE STATUS\G
*************************** 1. row ***************************
                Slave_IO_State: Waiting for master to send event
                Master_Host: 192.168.0.11
                Master_User: replication
                Master_Port: 3306
                Connect_Retry: 60
…
(db3) mysql> SHOW SLAVE STATUS\G
*************************** 1. row ***************************
                Slave_IO_State: Waiting for master to send event
                Master_Host: 192.168.0.11
                Master_User: replication
                Master_Port: 3306
                Connect_Retry: 60
…
(db4) mysql> SHOW SLAVE STATUS\G
*************************** 1. row ***************************
                Slave_IO_State: Waiting for master to send event
                Master_Host: 192.168.0.11
                Master_User: replication
                Master_Port: 3306
                Connect_Retry: 60
```
### 设置主主同步
db1到db2的同步
#### 查询db2的MASTER状态
```
mysql> SHOW MASTER STATUS;
```

| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
|------------------|----------|--------------|------------------|
| mysql-bin.000001 |      107 |              |                  |
|1 row in set (0.00 sec)|

#### 配置db1到db2的同步
```
(db1) mysql> CHANGE MASTER TO master_host = '192.168.0.12',master_port=3306,
master_user='replication',master_password='replication_password',
master_log_file='mysql-bin.000001',master_log_pos=107;
```
#### 启动db1的slave进程
```
(db1) mysql> START SLAVE;
```
#### 确认db1的同步状态
```
(db1) mysql> SHOW SLAVE STATUS\G
*************************** 1. row ***************************
                Slave_IO_State: Waiting for master to send event
                Master_Host: 192.168.0.12
                Master_User:
                Master_Port: 3306
                Connect_Retry: 60

```
所有同步状态确认ok，
在db1上写入数据会同步到 db2 db3 db4
在db2上写入数据会同步到 db1 db3 db4


## 创建MMM管理用户
现在我们来创建需要的用户，我们需要创建3个不同的用户

|功能|描述|权限
|:--|:--|:--|
|monitor user|mmm监控用于对mysql服务器进程健康检查|REPLICATION CLIENT
|agent user|mmm代理用来更改只读模式，复制的主服务器等等|SUPER, REPLICATION CLIENT, PROCESS
|relication user|用于复制|REPLICATION SLAVE
创建用户语句的代码如下：
```
GRANT REPLICATION CLIENT                 ON *.* TO 'mmm_monitor'@'192.168.0.%' IDENTIFIED BY 'monitor_password';
GRANT SUPER, REPLICATION CLIENT, PROCESS ON *.* TO 'mmm_agent'@'192.168.0.%'   IDENTIFIED BY 'agent_password';
GRANT REPLICATION SLAVE                  ON *.* TO 'replication'@'192.168.0.%' IDENTIFIED BY 'replication_password';
```
注意：就以上这些允许用户连接的主机而言我们可以设置更多的限制，比如mmm监控主机是从192.168.0.10连接的，mmm代理和复制是从192.168.0.11 - 192.168.0.14连接的，

## MMM安装部署
### 创建用户
可选的：创建MMM脚本和配置文件的所有者，这将能为安全地管理监控脚本提供更简单的方法。
```
useradd --comment "MMM Script owner" --shell /sbin/nologin mmmd
```
### 监控主机配置
#### 安装依存包
```
aptitude install liblog-log4perl-perl libmailtools-perl liblog-dispatch-perl libclass-singleton-perl libproc-daemon-perl libalgorithm-diff-perl libdbi-perl libdbd-mysql-perl
```
#### 安装MMM软件包
[MMM下载地址](http://mysql-mmm.org/downloads)
```
dpkg -i mysql-mmm-common_*.deb mysql-mmm-monitor*.deb
```
### 数据库主机配置
#### 安装依存包
```
aptitude install liblog-log4perl-perl libmailtools-perl liblog-dispatch-perl iproute libnet-arp-perl libproc-daemon-perl libalgorithm-diff-perl libdbi-perl libdbd-mysql-perl
```
#### 安装MMM软件包
[MMM下载地址](http://mysql-mmm.org/downloads)
```
dpkg -i mysql-mmm-common_*.deb mysql-mmm-agent_*.deb
```
### MMM文件配置
#### 通讯文件
mmm_common.conf
/etc/mysql-mmm/的文件权限要修改成640
编辑/etc/mysql-mmm/mmm_common.conf(所有主机的该文件都一样)
```
active_master_role          writer
    cluster_interface       eth0
    pid_path                /var/run/mmmd_agent.pid
    bin_path                /usr/lib/mysql-mmm/

    replication_user        replication
    replication_password    replication_password

    agent_user              mmm_agent
    agent_password          agent_password

    ip                      192.168.0.11
    mode                    master
    peer                    db2

    ip                      192.168.0.12
    mode                    master
    peer                    db1

    ip                      192.168.0.13
    mode                    slave

    ip                      192.168.0.14
    mode                    slave

    hosts                   db1, db2
    ips                     192.168.0.100
    mode                    exclusive

    hosts                   db1, db2, db3, db4
    ips                     192.168.0.101, 192.168.0.102, 192.168.0.103, 192.168.0.104
    mode                    balanced
```
拷贝 mmm_common.conf 到所有主机的/etc/mysql-mmm/ 目录下（5台主机都要拷贝）
#### 客户端文件
mmm_agent.conf
在数据库主机上我们需要编辑/etc/mysql-mmm/mmm_agent.conf文件，根据其他主机的不同更改db1的值（db2就将db1更改成db2）
```
include mmm_common.conf
this db1
```
### 监控文件
mmm_mon.conf
在监控主机上我们需要编辑/etc/mysql-mmm/mmm_mon.conf文件：
```
include mmm_common.conf

    ip                      127.0.0.1
    pid_path                /var/run/mmmd_mon.pid
    bin_path                /usr/lib/mysql-mmm/
    status_path             /var/lib/misc/mmmd_mon.status
    ping_ips                192.168.0.11, 192.168.0.12, 192.168.0.13, 192.168.0.14

    monitor_user            mmm_monitor
    monitor_password        monitor_password

debug 0
```
ping_ips选项是通过ping这些ip来确认监控机的网络连接是正常的，dubug修改为1 可以开启dubug模式，重启可见。

## 启动MMM客户端
### 配置系统代理
（在数据库服务器上）编辑/etc/default/mysql-mmm-agent来开启代理
```
ENABLED=1
```

### 启动服务
```
/etc/init.d/mysql-mmm-agent start
```
## 启动MMM监控端
### 配置系统代理
（在监控服务器上）编辑/etc/default/mysql-mmm-monitor文件来开启监控
```
ENABLED=1
```
### 修改bug
编辑 /usr/share/perl5/MMM/Monitor/Agent.pm 在41行处插入
```
if (! defined($old_state)) { $old_state = 'certinally not new_state'; }
```

### 启动服务
```
/etc/init.d/mysql-mmm-monitor start
```
等待几秒钟mmmd_mon启动，在几秒钟后你可以用mmm_control来检查群集的状态
```
  # mmm_control show
  db1(192.168.0.11) master/AWAITING_RECOVERY. Roles:
  db2(192.168.0.12) master/AWAITING_RECOVERY. Roles:
  db3(192.168.0.13) slave/AWAITING_RECOVERY. Roles:
  db4(192.168.0.14) slave/AWAITING_RECOVERY. Roles:
```
设置主机状态为在线状态
```
mmm_control set_online db1
mmm_control set_online db2
mmm_control set_online db3
mmm_control set_online db4

OK: State of 'db1' changed to ONLINE. Now you can wait some time and check its new roles!
OK: State of 'db2' changed to ONLINE. Now you can wait some time and check its new roles!
OK: State of 'db3' changed to ONLINE. Now you can wait some time and check its new roles!
OK: State of 'db4' changed to ONLINE. Now you can wait some time and check its new roles!
```

## 其他错误问题修改
### line 42错误
Use of uninitialized value $old_state in string ne at /usr/share/perl5/MMM/Monitor/Agent.pm line 42.
解决方法：
编辑 /usr/share/perl5/MMM/Monitor/Agent.pm 在41行处插入
```
if (! defined($old_state)) { $old_state = 'certinally not new_state'; }
```
### line 132错误
Starting MMM Agent Daemon: Configuration file /etc/mysql-mmm/mmm_agent.conf is world readable! at /usr/lib/perl5/vendor_perl/5.8.8/MMM/Common/Config.pm line 132
解决方法：
修改mysql-mmm目录的权限为640
```
chmod 640 /etc/mysql-mmm/*
```
