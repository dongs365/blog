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
 ## seafileltd
```
docker run -d --name seafile \
  -e SEAFILE_SERVER_HOSTNAME=seafile.mooom.top \
  -e SEAFILE_ADMIN_EMAIL=admin \
  -e SEAFILE_ADMIN_PASSWORD=aabbcc \
  -v /opt/seafile-data:/shared \
  -p 80:80 \
  seafileltd/seafile:latest
  ```
## nextcloud
```
docker run -d --name nextcloud \
    -p 800:80 \
    -v /root/nextcloud:/data \
    rootlogin/nextcloud
```
## vnc
```
docker run --name vnc \
    -itd \
    -p 5901:5901 \
    -p 6901:6901 \
    -e "VNC_PW=my-new-password" \
    -e VNC_RESOLUTION=1024x768 \
    consol/centos-xfce-vnc
```
## phpmyadmin
```
docker run --name phpmyadmin \
    -d \
    -e PMA_ARBITRARY=1 \
    -p 8080:80 \
    phpmyadmin/phpmyadmin
```
## openvpn-as
```
docker create --name=openvpn-as \
    --restart=always \
    -v /home/docker/openvpn-as/config:/config \
    -e INTERFACE=eth0 \
    -e PGID=1001 -e PUID=1001 \
    -e TZ=Asia/Shanghai \
    --net=host --privileged \
    linuxserver/openvpn-as
```
## jira
```
docker run --name jira \
    -d \
    -p 8080:8080 \
    cptactionhank/atlassian-jira-software:7.12.0
```
## confluence
```
docker run --name confluence \
    -d \
    -p 8090:8090 \
    cptactionhank/atlassian-confluence:6.13.0
```
## 2048
```
docker run --name 2048 \
  -d \ 
  -p 80:80 \
  alexwhen/docker-2048
  ```