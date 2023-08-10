---
title: Samba在linux上的使用
date: 2019-01-10
updated: 2019-01-10
toc: true
tags:
- 共享文件夹
- linux
---
## 1.安装

```shell
sudo yum -y install samba
```

## 2.配置

```conf
/etc/samba/smb.conf
eg:
[global]
workgroup = Administrators
netblos name =
server string = Linux Samba Server TestServer
security = share
display charset=UTF-8
unix charset=UTF-8
dos charset=UTF-8

[share] #====>windows下显示的共享文件夹名字：share
path = /opt
writeable = yes
browseable = yes
guest ok = yes
```

## 3.启动服务

```sh
service smb start
```

## 3. 开机自启动

```sh
[root@localhost ~]$ vi /etc/rc.local
#!/bin/sh
#
# This script will be executed *after* all the other init scripts.
# You can put your own initialization stuff in here if you don't
# want to do the full Sys V style init stuff.
service smb start
```

## 遇到的问题
* SELinux linux安全策略
```sh
# 1. 关闭SELinux

vim /etc/sysconfig/selinux
vim /etc/selinux/config

##把SELINUX=enforcing 改为 SELINUX=disabled

##临时关闭SELinux
setenforce 0

```
* 防火墙导致samba连接不了的问题
```sh
#2. 关闭防火墙
## 关闭iptables
/etc/init.d/iptables stop

## 永久关闭
chkconfig iptables off

## 检查
chkconfig --list iptables
```
* 文件夹权限
```sh
#3. 检查文件夹权限
chmod 777 /opt
```