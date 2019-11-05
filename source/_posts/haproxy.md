---
title: HAProxy
date: 2019-11-05 17:05:30
tags:
- 架构
categories: 负载均衡
---
HAProxy是一个使用C语言编写的自由及开放源代码软件，其提供高可用性、负载均衡，以及基于TCP和HTTP的应用程序代理。
<!-- more -->
## HAProxy 安装
### ubuntu
```
apt-get -y install haproxy
```
## HAProxy 配置
### 配置文件
```
vim /etc/haproxy/haproxy.cfg
```
### 开启web界面
```
listen admin_stats
        bind 0.0.0.0:1080               #监听端口
        mode http                       #http的7层模式
        option httplog                  #采用http日志格式
        #log 127.0.0.1 local0 err
        maxconn 10
        stats refresh 30s               #统计页面自动刷新时间
        stats uri /stats                #统计页面url
        stats realm admin\ Haproxy      #统计页面密码框上提示文本
        stats auth admin:123456         #统计页面用户名和密码设置
        stats hide-version              #隐藏统计页面上HAProxy的版本信息
```
### 配置实例
```
listen ssa
    bind 0.0.0.0:8839
    mode tcp
    server s1 192.168.5.21:8839 check inter 10000 rise 3 fall 3 weight 30
    server s2 192.168.5.22:8838 check inter 10000 rise 3 fall 3 weight 30
    server s3 192.168.5.23:8837 check inter 10000 rise 3 fall 3 weight 30
    
    #server node1 192.168.179.131:8081 check inter 2000 rise 3 fall 3 weight 30
    # check 开启健康检测
    # inter 2000 健康检查时间间隔2秒
    # rise 3 检测多少次才认为是正常的
    # fall 3 失败多少次才认为是不可用的
    # weight 30 权重
```
