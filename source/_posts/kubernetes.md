---
title: kubernetes
date: 2019-09-05 18:09:30
tags:
- k8s
categories: 集群
---
Kubernetes 是一个跨主机集群的 开源的容器调度平台，它可以自动化应用容器的部署、扩展和操作 , 提供以容器为中心的基础架构。
<!-- more -->
### 特点
便携性: 无论公有云、私有云、混合云还是多云架构都全面支持
可扩展: 它是模块化、可插拔、可挂载、可组合的，支持各种形式的扩展
自修复: 它可以自保持应用状态、可自重启、自复制、自缩放的，通过声明式语法提供了强大的自修复能力

### 核心组件组成
etcd保存了整个集群的状态；
apiserver提供了资源操作的唯一入口，并提供认证、授权、访问控制、API注册和发现等机制；
controller manager负责维护集群的状态，比如故障检测、自动扩展、滚动更新等；
scheduler负责资源的调度，按照预定的调度策略将Pod调度到相应的机器上；
kubelet负责维护容器的生命周期，同时也负责Volume（CVI）和网络（CNI）的管理；
Container runtime负责镜像管理以及Pod和容器的真正运行（CRI）；
kube-proxy负责为Service提供cluster内部的服务发现和负载均衡；
### 架构图示
![avatar](../../../../image/kubernetes.jpg)

## 部署指南
### 基础系统配置
#### 永久关闭swapoff
```
vim /etc/fstab
注释 swap 的那一行
```
#### 永久开启ip转发
```
sed -i "s/#net.ipv4.ip_forward/net.ipv4.ip_forward/g" /etc/sysctl.conf
sysctl -p
```

### 安装系统软件

#### docker 安装
```
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```
#### kubeadm 安装
```
apt-get update && apt-get install -y apt-transport-https
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y kubelet kubeadm kubectl
```
### PULL相关镜像


#### k8s镜像下载
```
#!/bin/bash
#获取images列表
#基础镜像
im1=`kubeadm config images list`
#ui镜像
im2=`curl -s https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml|grep image |awk '{print $2}'`
#网络镜像
im3=`curl -s https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml |grep image|grep amd64|uniq|awk '{print $2}'`
ki=`echo $im1 $im2 $im3`
#下载images镜像
for i in ${ki[*]};do docker pull ${i}; done
#新的images镜像名称
kia=(`for i in ${ki[*]};do echo $i | sed 's/k8s.gcr.io/registry.cn-shenzhen.aliyuncs.com\/grc/'; done`)
#修改images标签
for ((i=0;i<${#ki[@]};i++));do docker tag  ${ki[i]} ${kia[i]};done
#推送images
for i in ${kia[*]};do echo $i | docker push ${i} ; done
```

#### 本地PULL下载
```
#!/bin/bash
#获取images列表
#基础镜像
im1=`kubeadm config images list`
#ui镜像
im2=`curl -s https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml|grep image |awk '{print $2}'`
#网络镜像
im3=`curl -s https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml |grep image|grep amd64|uniq|awk '{print $2}'`
ki=`echo $im1 $im2 $im3`
#新的images镜像名称
kia=(`for i in ${ki[*]};do echo $i | sed 's/k8s.gcr.io/registry.cn-shenzhen.aliyuncs.com\/grc/'; done`)
#下载images镜像
for i in ${kia[*]};do docker pull ${i}; done
#修改images标签
for ((i=0;i<${#ki[@]};i++));do docker tag  ${kia[i]} ${ki[i]};done
```
#### 查看所需的images
```
kubeadm config images list
```
```
k8s.gcr.io/kube-apiserver:v1.13.2
k8s.gcr.io/kube-controller-manager:v1.13.2
k8s.gcr.io/kube-scheduler:v1.13.2
k8s.gcr.io/kube-proxy:v1.13.2
k8s.gcr.io/pause:3.1
k8s.gcr.io/etcd:3.2.24
k8s.gcr.io/coredns:1.2.6
```
```
k8s.gcr.io/kubernetes-dashboard-amd64:v1.10.1
quay.io/coreos/flannel：v0.11.0-amd64
```
#### 查看 dashboard 版本
```
https://raw.githubusercontent.com/kubernetes/dashboard/master/aio/deploy/recommended/kubernetes-dashboard.yaml
```
#### 查看 flannel 版本
```
https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

### k8s部署
#### 初始化
（指定k8s版本，内网ip段，apiserver的ip）
```
kubeadm init --kubernetes-version=1.15.0 --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=192.168.0.227
```
```
kubeadm init --pod-network-cidr=10.244.0.0/16
```
#### 重置kubeadm
```
kubeadm reset
```
```
rm -rf /var/lib/cni/ $HOME/.kube/config
```
### k8s插件安装
#### 安装网络
(注意配置文件中的network的网段)
```
sudo kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
sudo kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/k8s-manifests/kube-flannel-rbac.yml
```
#### dashboard安装
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-beta1/aio/deploy/recommended.yaml
kubectl proxy
```
https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/
https://github.com/kubernetes/dashboard/wiki/Creating-sample-user

#####  dashboard正式
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
```

###### 创建用户文件
dashboard-adminuser.yaml
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kube-system
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kube-system
```
```
kubectl apply -f dashboard-adminuser.yaml
```
##### 启动
```
kubectl proxy
```
##### 查看token
```
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
```

##### 访问地址
使用xshell把8001映射到8001
```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```