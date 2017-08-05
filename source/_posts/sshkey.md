---
title: SSH 使用密钥登陆
date: 2017-08-05 17:43:47
tags:
- 命令
- 网络
categories: Linux
---
SSH 是较可靠，专为远程登录会话和其他网络服务提供安全性的协议。利用 SSH 协议可以有效防止远程管理过程中的信息泄露问题。使用SSH密钥可以实现免密码登陆。
<!-- more -->
## 生成密钥
```
 ssh-keygen -t rsa -P
```
在~/.ssh下会生成公钥和私钥
authorized_keys:存放远程免密登录的公钥,主要通过这个文件记录多台机器的公钥
id_rsa : 生成的私钥文件
id_rsa.pub ： 生成的公钥文件
know_hosts : 已知的主机公钥清单

## 复制公钥到主机
```
ssh-copy-id -i ~/.ssh/id_rsa.pub root@10.1.15.111
```
会把id_rsa.pub中内容追加到远程主机~/.ssh/authorized_keys中


## 验证配置,正常登陆
```
ssh root@10.1.15.111
```

## 多个私钥配置
### 新增配置文件并修改权限
```
touch ~/.ssh/config
chmod 600 ~/.ssh/config
```

### 添加配置到配置文件
```
Host 10.1.15.*
    IdentityFile ~/.ssh/id_rsa.15
    User root

Host *.abc.com  
    IdentityFile ~/.ssh/id_rsa.abc
    User root  

Host github.com  
    IdentityFile ~/.ssh/id_rsa.github
    User git
```

### ssh-keygen 参数
ssh-keygen 用于为生成、管理和转换认证密钥，包括 RSA 和 DSA 两种密钥。
密钥类型可以用 -t 选项指定。如果没有指定则默认生成用于SSH-2的RSA密钥。

-a trials
        在使用 -T 对 DH-GEX 候选素数进行安全筛选时需要执行的基本测试数量。
-B      显示指定的公钥/私钥文件的 bubblebabble 摘要。
-b bits
        指定密钥长度。对于RSA密钥，最小要求768位，默认是2048位。DSA密钥必须恰好是1024位(FIPS 186-2 标准的要求)。
-C comment
        提供一个新注释
-c      要求修改私钥和公钥文件中的注释。本选项只支持 RSA1 密钥。
        程序将提示输入私钥文件名、密语(如果存在)、新注释。
-D reader
        下载存储在智能卡 reader 里的 RSA 公钥。
-e      读取OpenSSH的私钥或公钥文件，并以 RFC 4716 SSH 公钥文件格式在 stdout 上显示出来。
        该选项能够为多种商业版本的 SSH 输出密钥。
-F hostname
        在 known_hosts 文件中搜索指定的 hostname ，并列出所有的匹配项。
        这个选项主要用于查找散列过的主机名/ip地址，还可以和 -H 选项联用打印找到的公钥的散列值。
-f filename
        指定密钥文件名。
-G output_file
        为 DH-GEX 产生候选素数。这些素数必须在使用之前使用 -T 选项进行安全筛选。
-g      在使用 -r 打印指纹资源记录的时候使用通用的 DNS 格式。
-H      对 known_hosts 文件进行散列计算。这将把文件中的所有主机名/ip地址替换为相应的散列值。
        原来文件的内容将会添加一个".old"后缀后保存。这些散列值只能被 ssh 和 sshd 使用。
        这个选项不会修改已经经过散列的主机名/ip地址，因此可以在部分公钥已经散列过的文件上安全使用。
-i      读取未加密的SSH-2兼容的私钥/公钥文件，然后在 stdout 显示OpenSSH兼容的私钥/公钥。
        该选项主要用于从多种商业版本的SSH中导入密钥。
-l      显示公钥文件的指纹数据。它也支持 RSA1 的私钥。
        对于RSA和DSA密钥，将会寻找对应的公钥文件，然后显示其指纹数据。
-M memory
        指定在生成 DH-GEXS 候选素数的时候最大内存用量(MB)。
-N new_passphrase
        提供一个新的密语。
-P passphrase
        提供(旧)密语。
-p      要求改变某私钥文件的密语而不重建私钥。程序将提示输入私钥文件名、原来的密语、以及两次输入新密语。
-q      安静模式。用于在 /etc/rc 中创建新密钥的时候。
-R hostname
        从 known_hosts 文件中删除所有属于 hostname 的密钥。
        这个选项主要用于删除经过散列的主机(参见 -H 选项)的密钥。
-r hostname
        打印名为 hostname 的公钥文件的 SSHFP 指纹资源记录。
-S start
        指定在生成 DH-GEX 候选模数时的起始点(16进制)。
-T output_file
        测试 Diffie-Hellman group exchange 候选素数(由 -G 选项生成)的安全性。
-t type
        指定要创建的密钥类型。可以使用："rsa1"(SSH-1) "rsa"(SSH-2) "dsa"(SSH-2)
-U reader
        把现存的RSA私钥上传到智能卡 reader
-v      详细模式。ssh-keygen 将会输出处理过程的详细调试信息。常用于调试模数的产生过程。
        重复使用多个 -v 选项将会增加信息的详细程度(最大3次)。
-W generator
        指定在为 DH-GEX 测试候选模数时想要使用的 generator
-y      读取OpenSSH专有格式的公钥文件，并将OpenSSH公钥显示在 stdout 上。

~/.ssh/identity
        该用户默认的 RSA1 身份认证私钥(SSH-1)。此文件的权限应当至少限制为"600"。
        生成密钥的时候可以指定采用密语来加密该私钥(3DES)。

将在登录的时候读取这个文件。
~/.ssh/identity.pub
        该用户默认的 RSA1 身份认证公钥(SSH-1)。此文件无需保密。
        此文件的内容应该添加到所有 RSA1 目标主机的 ~/.ssh/authorized_keys 文件中。
~/.ssh/id_dsa
        该用户默认的 DSA 身份认证私钥(SSH-2)。此文件的权限应当至少限制为"600"。
        生成密钥的时候可以指定采用密语来加密该私钥(3DES)。

将在登录的时候读取这个文件。
~/.ssh/id_dsa.pub
        该用户默认的 DSA 身份认证公钥(SSH-2)。此文件无需保密。
        此文件的内容应该添加到所有 DSA 目标主机的 ~/.ssh/authorized_keys 文件中。
~/.ssh/id_rsa
        该用户默认的 RSA 身份认证私钥(SSH-2)。此文件的权限应当至少限制为"600"。
        生成密钥的时候可以指定采用密语来加密该私钥(3DES)。

将在登录的时候读取这个文件。
~/.ssh/id_rsa.pub
        该用户默认的 RSA 身份认证公钥(SSH-2)。此文件无需保密。
        此文件的内容应该添加到所有 RSA 目标主机的 ~/.ssh/authorized_keys 文件中。
/etc/ssh/moduli
        包含用于 DH-GEX 的 Diffie-Hellman groups 。
