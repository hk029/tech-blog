---
title: ssh免密码登录远程主机
tags:
- linux
- ssh
---

## 远程主机安装ssh

对于需要被远程连接的主机，你需要开启ssh服务，如果没有安装ssh，需要安装一下openssh-server

```
sudo apt-get update
sudo apt-get install openssh-server
```

检测启动

```
ps -e | grep ssh
```

启动命令

```
/etc/init.d/ssh start
```

## 配置

配置文件位于/etc/ssh/sshd_config，默认端口为22

```
Port 22
```

为了安全，可以选择其他端口，然后重启

```
/etc/init.d/ssh resart
```

## 权限控制

配置文件中：`AllowUsers`和`DenyUsers`可以控制用户登录权限

### 限制特定角色登录

```
DenyUsers root #不能以root登录
AllowUsers root #只有root用户可以登录
AllowUsers root,test #只有root，test用户可以登录
```

### 限制特定IP登录

```
AllowUsers *@192.168.0.1  #只有192.168.0.1可以登录
DenyUsers *@192.168.0.1  #只有192.168.0.1不可以登录
```

### 组合限制 

```
AllowUsers root@192.168.0.1  #只有该IP可以登录root
```

## 本地ssh连接远程主机

在本机上，可以通过`ssh`命令进行远程登录

```
ssh 远程主机用户@远程主机ip
```

如果连接成功，会提示你输入密码

## Sshkey免密码登录远程主机

为了避免在特定主机上每次都要输密码，我们通常会使用sshkey的方式进行登录。

### 生成ssh-key

首先，生成ssh-key，使用默认的配置，连续回车

```
ssh-keygen
```

你会发现在`~/.ssh/`文件夹下生成了`id_rsa.pub`和`id_rsa`文件，前者是公钥，后者是私钥。

### 将公钥复制到远程主机

运行命令，将当前生成的公钥复制到远程主机：

```
ssh-copy-id -i ~/.ssh/id_rsa.pub root@主机IP
```



### 尝试登录

再次运行ssh命令，会发现可以直接登录上远程主机而无需密码了

```
ssh 远程主机用户@远程主机ip 
```



### 分发私钥

如果你有不止一个主机需要进行远程登录，你可以把当前的私钥分发一份给其他主机，这里推荐使用`scp`命令，是远程复制指令：

```
scp ~/.ssh/id_rsa test@主机IP:/home/test/.ssh/
```



## 修改默认登录提示

登录提示是在`/etc/motd`中，可以修改：

```
vim /etc/motd
```

![image-20190409183740937](http://img.hksite.cn/2019-04-09-103746.png)