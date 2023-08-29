---
title: ssh免密登录
date: 2019-11-15
updated: 2019-11-15
toc: true
tags: 
- ssh
- linux
---

当我们在启动spark集群work节点的的时候，每次都需要输入每个节点服务器的密码，这样无疑是很麻烦的。设置SSH免密后就可跳过密码输入的步骤：
首先我们有多台服务器
### 生成SSH公钥

过程中直接回车就好

```shell
ssh-keygen -t rsa
```

### 分别将每台服务器生成的 /root/.ssh 目录下的公钥文件复制成 **authorized_keys**

```shell
cp id_rsa_pub authorized_keys
```

### 配置ssh免密登录

```shell
ssh-copy-id -i hostname # 或者ip
```
