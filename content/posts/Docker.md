---
title: Docker 笔记
date: 2020-11-11
updated: 2020-11-11 12:18:57
toc: true
comments: true
tags:
- docker
---

## 一、Docker介绍
***

### 1. 下载Docker依赖的环境

```shell
# 想安装Docker，需要先将依赖的环境全部下载下来，就像Maven依赖JDK一样
yum -y install yum-utils device-mapper-persistent-data lvm2
```

### 2. 指定Docker镜像源

```shell
# 默认下载Docker会去国外服务器下载，速度较慢，可以设置为阿里云镜像源，速度更快
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

```

### 3. 安装docker

```shell
yum makecache fast
yum -y install docker-ce
```

### 4. 启动Docker并测试

```shell
安装成功后，需要手动启动，设置为开机启动，并测试一下 Docker
#启动docker服务
systemctl start docker
#设置开机自动启动
systemctl enable docker
#测试
docker run hello-world
```

## 二、 Docker的中央仓库
*** 

```shell
# 1.Docker官方的中央仓库：这个仓库是镜像最全的，但是下载速度较慢。
https://hub.docker.com/
# 2.国内的镜像网站：网易蜂巢，daoCloud等，下载速度快，但是镜像相对不全。
https://c.163yun.com/hub#/home 
http://hub.daocloud.io/ （推荐使用）
# 3.在公司内部会采用私服的方式拉取镜像（添加配置）
#需要创建 /etc/docker/daemon.json，并添加如下内容
{
 "registry-mirrors":["https://registry.docker-cn.com"],
 "insecure-registries":["ip:port"]
}
# 重启两个服务
systemctl daemon-reload
systemctl restart docker
```

## 三、 镜像的操作
***

### 1. 拉取镜像

```shell
# 从中央仓库拉取镜像到本地
docker pull 镜像名称[:tag]
# 举个栗子:
docker pull daocloud.io/library/tomcat:8.5.15-jre8
```

### 2. 查看本地所有镜像

```shell
# 查看本地已经安装过的镜像信息，包含标识，名称，版本，更新时间，大小
docker images
```

### 3. 删除本地镜像

```shell
# 镜像会占用磁盘空间，可以直接手动删除，标识通过查看获取
docker rmi 镜像的标识
```

### 4. 镜像的导入导出

```shell
# 如果因为网络原因可以通过硬盘的方式传输镜像，虽然不规范，但是有效，但是这种方式导出的镜像名称和版本都是null，需要手动修改
# 将本地的镜像导出
docker save -o 导出的路径 镜像id
# 加载本地的镜像文件
docker load -i 镜像文件
# 修改镜像文件
docker tag 镜像id 新镜像名称：版本
```

## 四、 容器的操作
***

### 1. 运行容器

```shell
# 运行容器需要定制具体镜像，如果镜像不存在，会直接下载
#简单操作
docker run 镜像的标识|镜像的名称[:tag]
#常用的参数
docker run -d -p 宿主机端口:容器端口 --name 容器名称 镜像的标识|镜像名称[:tag]
#-d:代表后台运行容器
#-p 宿主机端口:容器端口：为了映射当前Linux的端口和容器的端口
#--name 容器名称:指定容器的名称
```

### 2. 查看正在运行的容器

```shell
# 查看全部正在运行的容器信息
docker ps [-qa]
#-a 查看全部的容器，包括没有运行
#-q 只查看容器的标识
```

### 3. 查看容器日志

```shell
# 查看容器日志，以查看容器运行的信息
docker logs -f 容器id
#-f：可以滚动查看日志的最后几行
```

### 4. 进入容器的内部

```shell
# 可以进入容器的内部进行操作
docker exec -it 容器id bash
```

### 5. 复制内容到容器

```shell
# 将宿主机的文件复制到容器内部的指定目录
docker cp 文件名称 容器id:容器内部路径
```

### 6. 重启&启动&停止&删除容器

```shell
容器的启动，停止，删除等操作，后续会经常使用到
#重新启动容器
docker restart 容器id
#启动停止运行的容器
docker start 容器id
 
#停止指定的容器(删除容器前，需要先停止容器)
docker stop 容器id
#停止全部容器
docker stop $(docker ps -qa)
#删除指定容器
docker rm 容器id
#删除全部容器
docker rm $(docker ps -qa)
```
## 五、Dockerfile
***

//todo

## 六、Docker Compose
***

//todo