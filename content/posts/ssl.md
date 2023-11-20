---
title: SSL证书之acme
date: 2023-09-22T10:23:34+08:00
draft: true
toc: true
images: 
tags:
  - untagged
---
## 安装ACME
```shell
curl https://get.acme.sh | sh
```
## 申请证书
申请证书前需要先确保`80`端口处于打开状态。
  
```shell
acme.sh --issue -d `your domain` --standalone -k ec-256
```
