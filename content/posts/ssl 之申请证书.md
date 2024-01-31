---
title: SSL证书之acme
date: 2023-09-22T10:23:34+08:00
draft: false
toc: true
images: 
tags:
  - SSL
---
## 安装ACME
```shell
curl https://get.acme.sh | sh
# 安装socat：
apt install socat
# 添加软链接：
ln -s /root/.acme.sh/acme.sh /usr/local/bin/acme.sh
# 注册账号： 
acme.sh --register-account -m my@example.com
# 开放80端口：
ufw allow 80
```
## 申请证书
申请证书前需要先确保`80`端口处于打开状态。
  
```shell
# 申请证书
acme.sh --issue -d `your domain` --standalone -k ec-256
# 安装证书： 
acme.sh --installcert -d `your domain` --ecc \
--key-file /root/trojan/server.key \
--fullchain-file /root/trojan/server.crt
# 如果默认CA无法颁发，则可以切换下列CA： 
# 切换 Let’s Encrypt：
acme.sh --set-default-ca --server letsencrypt 
# 切换 Buypass：
acme.sh --set-default-ca --server buypass 
# 切换 ZeroSSL：
acme.sh --set-default-ca --server zerossl
```
