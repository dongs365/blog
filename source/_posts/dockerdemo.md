---
title: docker镜像实例
date: 2018-09-10 11:49:26
tags:
  - docker
categories: linux
---
docker 常用镜像实例 <!-- more -->

## jenkins
```
docker run \
  -u root \
  --rm \
  -d \
  -p 8080:8080 \
  -v jenkins-data:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  jenkinsci/blueocean
```
## gitlab
```
docker run --detach \
    --hostname gitlab.example.com \
    --publish 443:443 --publish 80:80 --publish 22:22 \
    --name gitlab \
    --restart always \
    --volume /srv/gitlab/config:/etc/gitlab \
    --volume /srv/gitlab/logs:/var/log/gitlab \
    --volume /srv/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce:latest
```
## shadowsocks
```
docker run \
    -d \
    -p 8388:8388 \
    --restart=always \
    oddrationale/docker-shadowsocks \
    -s 0.0.0.0 \
    -p 7749 \
    -k password \
    -m aes-256-cfb
```
## mysql
```
docker run --name mysql-server -t \      
    -e MYSQL_DATABASE="dataname" \     
    -e MYSQL_USER="user" \      
    -e MYSQL_PASSWORD="passwd" \      
    -e MYSQL_ROOT_PASSWORD="root_pwd" \
    -d mysql:5.7
```
