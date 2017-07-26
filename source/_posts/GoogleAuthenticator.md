---
title: GoogleAuthenticator系统二次验证
date: 2017-07-31 10:21:57
tags:
- 安全
- 工具
categories: Linux
---
Google Authenticator项目包括为多个移动平台实现一次性密码生成器。一次性密码是由开放认证计划）开发的开放标准生成的 。可以和linux结合进行登陆二次验证，进行动态验证。
<!-- more -->
#### 服务端安装
apt自动安装
```
apt-get install libpam-google-authenticator
```
github手动安装
```
git clone https://github.com/google/google-authenticator.git
cd google-authenticator/libpam/
./bootstrap.sh
./configure
make
make install
```
#### 安装QrenCode
命令行下生成二维码
```
wget https://fukuchi.org/works/qrencode/qrencode-3.4.4.tar.gz
tar zxvf qrencode-3.4.4.tar.gz
cd qrencode
apt-get install pkg-config pkgconfi libpng-dev
./configure --prefix=/usr && make && make install
```


#### 手机端安装

手机端搜索安装Google Authenticator安装即可

[android版](https://play.google.com/store/apps/details?id=com.google.android.apps.authenticator2&hl=zh_CN)

[iOS版](https://itunes.apple.com/cn/app/google-authenticator/id388497605)



#### 生成二维码
```
google-authenticator
Do you want authentication tokens to be time-based (y/n) y
```
生成二维码和5个备用验证码(记的保存)
```
Do you want me to update your "/home/ubuntu/.google_authenticator" file (y/n) y  
提示是否要更新验证文件，选择y

Do you want to disallow multiple uses of the same authentication
token? This restricts you to one login about every 30s, but it increases
your chances to notice or even prevent man-in-the-middle attacks (y/n) y
您是否拒绝多次使用相同的身份验证令牌？y

By default, tokens are good for 30 seconds and in order to compensate for
possible time-skew between the client and the server, we allow an extra
token before and after the current time. If you experience problems with poor
time synchronization, you can increase the window from its default
size of 1:30min to about 4min. Do you want to do so (y/n) y
默认动态验证码在30秒内有效，由于客户端和服务器可能会存在时间差，可将时间增加到最长4分钟，是否要这么做 y

If the computer that you are logging into isn't hardened against brute-force
login attempts, you can enable rate-limiting for the authentication module.
By default, this limits attackers to no more than 3 login attempts every 30s.
Do you want to enable rate-limiting (y/n) y
是否限制尝试次数，每30秒只能尝试最多3次，这里选择y进行限制
```


#### 添加到手机端
```
打开手机Authenticator
扫描条形码或者手动输入验证码，绑定刚刚生成的密钥
```
#### 修改登陆配置
```
vim /etc/pam.d/sshd
添加 auth required pam_google_authenticator.so

vim /etc/ssh/sshd_config
查  找 ChallengeResponseAuthentication no
修改为 ChallengeResponseAuthentication yes
```

#### 重启ssh服务
```
service ssh restart
```
#### 验证登陆
```
输入账号之后，会提示输入验证码
login as: ubuntu
Using keyboard-interactive authentication.
Verification code:
打开手机上的Google身份验证器，输入动态验证码，回车。
```
