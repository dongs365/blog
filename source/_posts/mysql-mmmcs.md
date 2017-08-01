---
title: mysql-mmm参数说明
date: 2017-08-02 18:42:08
tags:
- mysql
- 架构
categories: 数据库
---
MySQL-mmm有关mmm_control参数测试
<!-- more -->
## MMM主要功能脚本介绍：
### mmm_mond
  监控进程，负责所有的监控工作，决定和处理所有节点角色活动。
###  mmm_agent
  运行在每个mysql服务器上的代理进程，完成监控的探针工作和执行简单的远端服务设置。
### mmm_control
   一个简单的脚本，提供管理mmm_mond进程的命令。
## MMM_MOND参数介绍
### status
#### mmm_control show
运行mmm_control show时monitor监控的mysql服务可以有如下几种状态：
online  主机正在运行，没有任何问题。
admin_offline  手动被设置成离线状态。
hard_offline  主机不在线(ping检测或者是mysql检测失败)。
awating_recovery  主机即将恢复在线。
replication_delay  复制延迟很大，积压了很多未完成的日志(rep_backlog检测失败)。
replication_fall  复制线程没有运行(rep_threads检测失败)。
#### mmm_common.conf
配置文件auto_set_online参数大小设置问题：

当mmm_mon.conf文件的auto_set_online 10参数设置小于60时db1、db2mysql服务停止启动切换时，不会出现上述AWAITING_RECOVERY状态，只要mysql服务启动即会自动切换到online状态，当配置参数等于或者大于60时，即会出现如下状态，

当用mmm_control set_onlinedb1即可修改db1为在线online状态。
总结：只有当主机在线的时候才能获得角色(reader or writer)，当一个主机从online转换到其它状态，它所获得的角色都会从它身上移除。
  一个主机在replication_delay或者是replication_fall状态时，如果所有的问题都解决了，就会被再次切换到online状态，除非它的状态在抖动，不稳定。参见flapping(抖动)。
  一个主机在hard_offline状态，如果所有的问题都解决了，那么它会转换到awaiting_recovery状态，如果它的故障时间小于60s,并且它没有重启或者auto_set_online>0,那么它将会被自动切换到online状态，除非它的状态在抖动，不稳定。参见flappping(抖动)。
  如过活动的master出现积压了很多未完成的复制工作或者复制失败将不被视为一个问题，所以活动的master不会被置于replication_delay或者REPLICATION_FAIL状态。
  在节点被转换到ONLINE状态60s内，如果出现复制失败或者出现积压了很多未完成的复制工作将会被忽略(这个时间通过master-connect-retry来控制)。
  如果rep_backlog和rep_threads都检测失败，将会切换到REPLICATION_FAIL状态。

### ROLES
 在mmm_common.conf

exclusive roles
  互斥角色只有一个ip，并且同一时间只能分配给一个主机，你可以指定一个优先(preferred)主机，如果这个主机是ONLINE状态，那么角色就会被切换到这个主机。
balanced roles
  负载均衡角色可以有多个IP，这些IP被均衡的分配给多个主机，所以没有一个主机可以比其他主机多出两个角色。
（备忘录：可以通过给可写的active_master_role设置mode=slave把角色切换给其他master）。
### CHECKS
 mmm_controlcheck

mmm_mond会对每个主机执行4项检查，并决定检查是否成功：
ping   -- ----主机可以ping通。
mysql   -- ----mysql server在线
rep_threads   -- ----复制线程在运行
rep_backlog   -- ----复制积压的日志很少

### MODES
#### ACTIVE MODE
Monitor将会自动的把角色从失败的主机上移除，并切换到其他主机上。
#### MANUAL MODE
Moniter会自动把负载均衡的角色分配给对应主机，但是不会自动的把角色从失败的主机上移除。你可以通过move_role来手工移除。
#### WAIT MODE
类似manual模式，但是当两个master都是online状态或者超过了wait_for_other_master的时间，将被改变为ACTIVE模式。
#### PASSIVE MODE
在被动模式下，monitor不改变roles，也不更新状态文件和发送任何信息给agents。在被动模式下你可以使用set_ip来改变roles，但是这些改变在monitor切换到ACTIVE或者MANUAL模式(set_active or set_manual)前都不会生效,在启动的roles发生冲突将会进入被动模式。你可以分析问题，修复reole信息，然后切换到ACTIVE或者MANUAL模式，也可以手动进去被动模式，使用 set_passive。

### mmm_control有关参数解析
#### help
查看有关帮助的信息help
```
mmm_control help
```
#### ping
ping monitor的进程，确定进程是否存在：
```
mmm_control ping
```
如果monitor宕掉后，再ping就会报错：
```
mmm_control ping
```
ERROR: Can'tconnect to monitor daemon!
#### show
查看当前的集群状态：
```
mmm_control show
```
#### checks
 查看指定node或者所有主机的制定状态或者所有状态。
mmm_control checks
mmm_control checks db1
mmm_control checks db1 rep_backlog

mmm_control checks db1 rep_backlog
#### set_online host
set_online的作用是把node的状态从AWAITING_RECOVERY或者ADMIN_OFFLINE恢复到ONLINE，如下：db2主机被重启过，查看状态：
当db2检查时候后，所有的角色都被移动到db1和db3、db4上，现在我们恢复db2到online状态：
```
mmm_control set_online db2
```

然后查看db2的状态如下：

#### set_offline host
set_offline会将一个node手动的置为ADMIN_OFFLINE状态，这个node上的所有角色将会被移除，并且停止mysql复制。
```
mmm_control set_offline db1
```

#### mode
打印出当前monitor的运行模式，如：
```
mmm_control mode
```

#### set_active 主动模式
切换monitor进程为ACTIVE模式：
```
mmm_control set_active
```
#### set_manual 收到模式
切换monitor 进程为manual模式
```
mmm_control set_manual
```
#### set_passive 被动模式
切换monitor进程为passive模式
```
mmm_control set_passive
```
#### move_role
通过move可以在集群nodes之间切换独一的角色，这个命令在passive模式下不可用。
```
mmm_control move_role writer db1
```
修改为手动模式，或者自动模式即可使用角色切换命令：
例如：我们把writer角色切换到db1主机上：
#### move_role   -force role host
可以将active_master_role角色切换给一个状态为replication_fall或者replication_delay的主机上，使用这个命令一定要小心，这个命令在被动模式下无效。
#### set_ip ip host
set_ip可以用来在被动模式下操作角色，改变在monitor改变为active或者manual模式后生效。

### mmm_control 控制monitor
#### config file
config 文件可以放在/etc; /etc/mmm; /etc/mysql-mmm这三个目录中， /etc/mysql-mmm优先级最高；
程序名  文件名
mmm_agentd    'mmm_agent.conf'
mmm_mond     'mmm_mon.conf'
mmm_control    'mmm_mon.conf'
参考下图：
#### COMMENTS
每行以#开头的行被认为是注释，并且这行会被忽略。注释不可以出现在一行注释的后面。注释前面的空格将会被忽略。
### This is acomment
debug 1 # this isno comment
7.2.2 VARIABLES
debug 0
#### SPECIFYING MULTIPLE VALUES
有些变量你需要指定多个值：
ips192.168.0.51,192.168.0.52,192.168.0.53
#### SECTIONS
配置文件分为两种类型的sestions：唯一的和命名的。
唯一型的sections，例如：
<monitor>
ip 127.0.0.1
< /monitor>
定义一个空的唯一型sections也是可以的：
<socket />
命名型的sections,例如：
<host db1>
ip 192.168.0.31
< /host>
你当然也可以定义一个空的命名型sections：
<checkmysql/>
default sections
默认sections里面的值会被其他所有的sections继承。
定义所有的主机角色都是'slave'
< hostdefault>
mode slave
< /host>
#### INCLUDES
可以通过include导入其他配置文件
include common.conf
### CONFIGURATION VARIABLES
#### 全局变量
this
描述：这个主机的名字
默认值：无
Used by：agent，tools
debug
描述：是否开启debug模式
可用值：true/yes/1/on flase/no/0/off
默认值：0
Used by：agent，monitor
active_master_role
描述：哪个角色名被定义为活动的master
默认值：无
Used by：agent，monitor
max_kill_retries
描述：尝试杀死进程的最大次数，用来防止在切换active_master_role时产生多余的写操作
默认值：10
Used by：agent
default_copy_method
描述：默认拷贝方法的名字
默认值：无
Used by：tools
clone_dirs
描述：保存mysql日志和数据的路径(相对逻辑卷的挂载点)
默认值：无
Used by：tools
#### MONITORSECTION
monitor section需要mmm_mond和mmm_control
IP
描述：mmm_mond进程监听哪个IP
默认值：无
Used by：control，monitor
port
描述：mmm_mond进程监听哪个端口
默认值：无
Used by：control，monitor
pid_path
描述：pid-file的路径
默认值：无
Used by： monitor
bin_path
描述：包含mmm二进制文件的目录
默认值：无
Used by：agent
status_path
描述：状态文件的位置
默认值：无
Used by：monitor
ping_interval
描述：网络监测的间隔时间
默认值：1s
Used by：monitor
ping_ips
描述：网络监测使用的Ips
默认值：
Used by：monitor
flap_duration
描述：抖动检测持续的时间
默认值：3600
Used by：monitor
flap_count
描述：在flap_duration时间内出现flap_count次宕机，我们就认为这个时机为抖动状态
默认值：3
Used by：monitor
auto_set_online
描述：从AWAITING_RECOVERY状态切换到ONLINE状态的时间，单位秒，0=disabled
默认值：0
Used by：monitor
kill_host_bin
描述：当一个主机不可达，并且角色不能从一个agent上删除，就需要一个二进制命令用来杀掉一个主机，这个命令需要你自己定制，第一个参数是主机，第二个参数是ping的状态(1 ok,0 not ok)
默认值：bin_path/monitor/kill_host
Used by：monitor
careful_startup
描述：小心的启动，例如当writer角色被配置在多个主机上时进入passive模式
Allowed values：true/yes/1/onfalse/no/0/off
默认值：0
Used by：monitor
mode
描述：monitor默认的模式
Allowed values：activemanual wait passive
默认值：active
Used by：monitor
wait_for_other_master
描述：从WAIT模式切换到ACTIVE模式时，等待其他master切换到ONLINE状态的时间，单位秒，0=永远等待
默认值：120
Used by：monitor
#### HOSTSECTIONS
ip
描述：主机的IP
默认值：0
Used by：agent，monitor，tools
mode
描述：主机的模式，只有master和slave模式
默认值：0
Used by：agent，monitor
peer
描述：对等的主机名（只有在master主机才有）
默认值：0
Used by：agent，monitor
pid_path
描述：pid_file的位置
默认值：0
Used by：agent
bin_path
描述：包含mmm工具的目录
默认值：0
Used by：agent
agent_port
描述：mmm_agent监听的端口
默认值：9989
Used by：agent
cluster_interface
描述：集群的虚拟IP配置在哪个网卡上
默认值：无
Used by：agent
mysql_port
描述：mysqld监听的端口
默认值：3306
Used by：agent，monitor，tools
mysql_pidfile
描述：mysql的pidfile位置
默认值：/var/run/mysqld/mysqld.pid
Used by：tools
mysql_rcscript
描述：mysql rc-script的位置
默认值：/etc/init.d/mysql
Used by：tools
mysql_cnf
描述：my.cnf文件的位置
默认值：/etc/my.cnf
Used by：tools
agent_user
描述：MMM agent 使用的mysql用户
默认值：无
Used by：agent
agent_password
描述：MMM agent 使用的mysql用户的密码
默认值：无
Used by：agent
monitor_user
描述：MMM monitor 使用的mysql用户
默认值：无
Used by：monitor
monitor_password
描述：MMM monitor 使用的mysql用户的密码
默认值：无
Used by：monitor
replication_user
描述：mysql复制使用的用户
默认值：无
Used by：agent，tools
replication_password
描述：mysql复制使用的用户的密码
默认值：无
Used by：agent，tools
ssh_user
描述：MMM tools 使用的SSH用户
默认值：无
Used by：tools
ssh_port
描述：MMM tools 使用的SSH端口
默认值：无
Used by：tools
ssh_parameters
描述：MMM tools 使用的SSH时的额外参数
默认值：无
Used by：tools
tools_user
描述：MMM tools 使用的mysql用户
默认值：无
Used by：tools
tools_password
描述：MMM tools 使用的mysql用户的密码
默认值：无
Used by：tools
backup_dir
描述：备份的目标目录
默认值：无
Used by：tools
restor_dir
描述：还原的源目录
默认值：无
Used by：tools
lvm_bin_lvcreate
描述：lvcreate二进制文件的路径
默认值：lvcreate
Used by：tools
lvm_bin_lvremove
描述：lvremove二进制文件的路径
默认值：lvremove
Used by：tools
lvm_snapshot_size
描述：lvm快照的大小
默认值：
Used by：tools
lvm_logical_volume
描述：mysql数据和日志所在的lvm逻辑卷的名字
默认值：
Used by：tools
lvm_volume_group
描述：mysql数据和日志所在的lvm逻辑卷组的名字
默认值：
Used by：tools
lvm_mount_dir
描述：lvm快照的挂载点
默认值：
Used by：tools
lvm_mount_ops
描述：lvm快照挂在使用的参数
默认值：
Used by：tools
#### ROLESECTIONS
这个sections定义cluster中有哪些角色，虚拟IP与角色的对应关系。每个角色这些服务器之间切换，每个服务器需要一个IP(用于monitor和此服务器通信)，每个角色也需要一个IP。
mode
描述：角色的模式，分为balanced和exclusive两种模式。（一般writer角色是exclusive模式，reader角色是balanced模式）
默认值：
Used by：monitor
hosts
描述：哪些主机可以担任这个角色
默认值：
Used by：monitor
ips
描述：lvm快照挂在使用的参数
默认值：一个或者多个对应角色的虚拟IP
Used by：monitor
prefer
描述：那个主机对此角色具有较高优先级，只能使用在exclusive模式
默认值：
Used by：monitor
#### CHECK SECTIONS
check_period
描述：检查周期默认为5s
默认值：5s
Used by：monitor
trap_period
描述：一个节点被检测不成功的时间持续trap_period秒，就慎重的认为这个节点失败了。
默认值：10s
Used by：monitor
timeout
描述：检查超时的时间
默认值：2s
Used by：monitor
restart_after
描述：在完成restart_after次检查后，重启checker进程
默认值：10000
Used by：monitor
max_backlog
描述：记录检查rep_backlog日志的最大次数
默认值：60
Used by：monitor
#### SOCKET SECTION
type
描述：socket的类型是普通的还是ssl
默认值：
Used by：agent，control，monitor
cert_file
描述：如果使用ssl类型的连接，指定本地证书的位置
默认值：
Used by：agent，control，monitor
key_file
描述：RSA私钥的位置(使用ssl连接时使用)
默认值：
Used by：agent，control，monitor
ca_file
描述：包含著名的证书颁发结构颁发的证书文件的位置(使用ssl连接时使用)
默认值：
Used by：agent，monitor
#### COPY_METHOD SECTIONS
backup_command
描述：用来创建备份的命令
默认值：
Used by：tools
restor_command
描述：用来还原备份的命令
默认值：
Used by：tools
incremental_command
描述：显示可用的增量备份
默认值：
Used by：tools
incremental
描述：表明增量备份是否支持copy方法
默认值：0
Used by：tools
single_run
描述：决定拷贝方法是否可以拷贝所有的目录在一个进程
默认值：
Used by：tools
true_copy
描述：决定拷贝方法是否可以创建一个1:1的拷贝
默认值：
Used by：tools
### LOGGING
#### LOGGING CONFIG FILES
MMM使用Log4perl记录日志，所以日志相当灵活，如果默认的日志不是你需要的，你可以创建一个配置文件放在/etc/mmm or /etc/mysql-mmm, /etc/mysql-mmm优先级更高
程序对应依耐的配置文件如下：
程序配置文件名
mmm_agentd‘mmm_agent_log.conf’
mmm_mond‘mmm_mon_log.conf’or ‘mmm_mon_log_CLUSTER.conf’
mmm_control‘mmm_mon_log.conf’or ‘mmm_mon_log_CLUSTER.conf’
mmm_backup
mmm_clone
mmm_restore
#### LOG4PERL SAMPLE CONFIGURATION
这里是默认的日志配置
log4perl.logger =INFO, LogFile
log4perl.appender.LogFile= Log::Log4perl::Appender::File
log4perl.appender.LogFile.Threshold= INFO
log4perl.appender.LogFile.filename= /var/log/mysql-mmm/progam.info
log4perl.appender.LogFile.recreate= 1
log4perl.appender.LogFile.layout= PatternLayout
log4perl.appender.LogFile.layout.ConversionPattern= %d %5p %m%n
如果以debug模式运行，添加如下信息:
log4perl.logger =DEBUG, ScreenLog, LogFile
log4perl.appender.ScreenLog= Log::Log4perl::Appender::Screen
log4perl.appender.ScreenLog.stderr= 0
log4perl.appender.ScreenLog.layout= PatternLayout
log4perl.appender.ScreenLog.layout.ConversionPattern= %d %5p %m%n
通常可以把日志记录到多个文件中(以不同的重要级别来分离)，或者让MMM给你发送邮件（在2.1.1版本下默认的日志配置是这样的）
log4perl.logger =INFO, FileInfo, FileWarn, FileError, FileFatal, MailFatal
log4perl.appender.FileInfo= Log::Log4perl::Appender::File
log4perl.appender.FileInfo.Threshold= INFO
log4perl.appender.FileInfo.filename= /var/log/mysql-mmm/progam.info
log4perl.appender.FileInfo.recreate= 1
log4perl.appender.FileInfo.layout= PatternLayout
log4perl.appender.FileInfo.layout.ConversionPattern= %d %5p %m%n
log4perl.appender.FileWarn= Log::Log4perl::Appender::File
log4perl.appender.FileWarn.Threshold= WARN
log4perl.appender.FileWarn.filename= /var/log/mysql-mmm/progam.warn
log4perl.appender.FileWarn.recreate= 1
log4perl.appender.FileWarn.layout= PatternLayout
log4perl.appender.FileWarn.layout.ConversionPattern= %d %5p %m%n
log4perl.appender.FileError= Log::Log4perl::Appender::File
log4perl.appender.FileError.Threshold= ERROR
log4perl.appender.FileError.filename= /var/log/mysql-mmm/progam.error
log4perl.appender.FileError.recreate= 1
log4perl.appender.FileError.layout= PatternLayout
log4perl.appender.FileError.layout.ConversionPattern= %d %5p %m%n
log4perl.appender.FileFatal= Log::Log4perl::Appender::File
log4perl.appender.FileFatal.Threshold= FATAL
log4perl.appender.FileFatal.filename= /var/log/mysql-mmm/progam.fatal
log4perl.appender.FileFatal.recreate= 1
log4perl.appender.FileFatal.layout= PatternLayout
log4perl.appender.FileFatal.layout.ConversionPattern= %d %5p %m%n
log4perl.appender.MailFatal= Log::Dispatch::Email::MailSend
log4perl.appender.MailFatal.Threshold= FATAL
log4perl.appender.MailFatal.to= root
log4perl.appender.MailFatal.buffered= 0
log4perl.appender.MailFatal.subject= FATAL error in progam
log4perl.appender.MailFatal.layout= PatternLayout
log4perl.appender.MailFatal.layout.ConversionPattern= %d %m%n
