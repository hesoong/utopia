---
title: spark 单机环境搭建
date: 2019-11-13
updated: 2019-11-13
toc: true
tags: 
- spark 
- 环境搭建
---

## 环境需要

- java环境部署
- spark环境部署

## java环境部署（安装jdk）

参考：[linux下java环境部署]({{< ref "java-deployment-under-linux" >}})

## spark 环境部署

### spark安装包下载

-->[spark官网下载地址](http://spark.apache.org/downloads.html)<--

这里我下载的2.4.4版本:`spark-2.4.4-bin-hadoop2.7.tgz`

### 解压缩

将下载的tgz包放到自定义路径下，我这里放到的 **/opt** 下
然后就是解压了，linux下的解压命令是

```shell
tar -zxvf spark-2.4.4-bin-hadoop2.7.tgz
```

为了操作方便，将解压得到的spark-2.4.4-bin-hadoop2.7文件夹改名为spark-2.4.4：

```shell
mv spark-2.4.4-bin-hadoop2.7 spark-2.4.4
```

### 配置环境变量

配置系统的环境变量：编辑 **/etc/profile** 文件，若执行命令提示没有 **vim** 时，用 **vi** 代替即可，也可以用如下命令安装即可

```shell
 yum -y install vim
```

编辑环境变量：

```shell
vim  /etc/profile
```

然后在文件的最后加上如下配置：

```shell
#Spark
export SPARK_HOME=/opt/spark-2.4.4
export PATH=$PATH:$SPARK_HOME/bin
#Spark end
```

重新加载文件让其生效

```shell
source /etc/profile
```

然后键盘按下 **“Esc”** 键退出编辑模式，输入 **:wq** 保存即可

重新加载文件让其生效

```shell
source /etc/profile
```

### 配置spark的配置文件之单机版

进入%SPARK_HOME%/conf路径，分别拷贝两个配置文件

```shell
cd /opt/spark-2.4.4/conf
# 分别拷贝两个配置文件
cp spark-env.sh.template spark-env.sh
cp slaves.template  slaves
```

编辑spark-env.sh文件：

```shell
export SPARK_MASTER_IP=192.168.1.210
export SPARK_MASTER_PORT=7077
```

编辑slaves文件：

```shell
localhost
```

测试环境配置是否成功

```shell
cd /opt/spark-2.4.4/bin/
./run-example   SparkPi   10
```

出现如下图所示字样即配置成功

![spark测试](spark测试.png)

启动spark，bin目录下执行

```shell
./spark-shell
```

### 配置spark的配置文件之单机版集群版

spark 的一些默认端口

```yml
8080: master的webUI，sparkwebUI的端口
7077: 提交任务的端口
8081: worker的webUI的端口
18080: historyServer的webUI的端口
4040: application的webUI的端口
```
