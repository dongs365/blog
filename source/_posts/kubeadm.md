---
title: kubeadm
date: 2019-09-06 10:20:20
tags:
- k8s
categories: 集群
---
kubeadm 能帮助您建立一个小型的符合最佳实践的 Kubernetes 集群。
<!-- more -->
通过使用 kubeadm, 您的集群会符合 Kubernetes 合规性测试的要求. Kubeadm 也支持其他的集群生命周期操作，比如升级、降级和管理启动引导令牌。
### 系统初始化配置
#### 关闭swap
```
vim /etc/fstab
注释 swap 的那一行
```
#### 开启转发
```
sed -i "s/#net.ipv4.ip_forward/net.ipv4.ip_forward/g" /etc/sysctl.conf
sysctl -p
```

### 安装kubeadmin
#### Debian/Ubuntu 阿里云源
```
apt-get update && apt-get install -y apt-transport-https
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y kubelet kubeadm kubectl
```

### kubeadm命令
#### 查看镜像列表
```
kubeadm config images list
```
#### 下载镜像
```
kubeadm config images pull
```
#### 查看指定版本镜像
```
kubeadm config images list --kubernetes-version v1.15.1
```
#### 下载指定版本镜像
```
kubeadm config images pull --kubernetes-version v1.15.1
```
#### pull镜像命令生成
```
kubeadm config  images list |awk  '{print "docker pull " $1 }'
```
#### tag命令生成
```
kubeadm config  images list |awk -F"/" '{print "docker tag " $1"/"$2 " registry.cn-shenzhen.aliyuncs.com/grc/"$2}'
```
#### push命令生成
```
kubeadm config  images list |awk -F"/" '{print "docker push " $1"/"$2 " registry.cn-shenzhen.aliyuncs.com/grc/"$2}'
```
#### kubeadm 初始化参数
```
kubeadm init \
#masterip
--apiserver-advertise-address=10.0.52.13 \
#镜像仓库
--image-repository registry.aliyuncs.com/google_containers \
#版本设置
--kubernetes-version v1.13.3 \
#网络设置
--service-cidr=10.1.0.0/16 \
#网络设置
--pod-network-cidr=10.244.0.0/16
```
#### 重置安装
```
kubeadm reset
```