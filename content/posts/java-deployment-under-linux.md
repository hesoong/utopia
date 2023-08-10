---
title: linux下java环境部署
date: 2019-01-13 13:03:24
updated: 2019-01-13
toc: true
tags:
- java
- 环境搭建
categories: 
- 工作与技术
---

## 下载JDK安装包

-->[jdk下载官网](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)<--

-->[jdk历史版本下载](https://www.oracle.com/technetwork/java/javase/downloads/java-archive-javase8-2177648.html)<--

根据自己的系统版本下载*.tar.gz结尾的压缩包， 我这里下载的是 jdk-8u151-linux-i586.tar.gz 安装包

## 解压缩

将下载的安装包放到自定义路径下，我这里放到的 **/opt** 下
然后就是解压了，linux下的解压命令是

```shell
tar -zxvf jdk-8u151-linux-i586.tar.gz
```

## 配置环境变量

配置系统的环境变量：编辑 **vim /etc/profile** 文件，若执行命令提示没有 **vim** 时，用 **vi** 代替即可，也可以用如下命令安装即可

```shell
 yum -y install vim
```

编辑环境变量：

```shell
vim  /etc/profile
```

然后在文件的最后加上如下配置：

```shell
#java
export JAVA_HOME=/opt/jdk1.8.0_151
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib
export PATH=$JAVA_HOME/bin:$PATH
#java end
```

重新加载文件让其生效

```shell
source /etc/profile
```

## 检查是否配置成功

```shell
java -verison
```

如下显示即安装成功

![检查配置](检查配置.png)
