---
title: linux的一些零星记录
date: 2019-01-30
updated: 2019-01-30
toc: true
tags:
    - linux

---

## 1. linux忘记root密码

[解决办法](https://www.cnblogs.com/kerrycode/p/3479925.html)

## 2. java环境变量配置

1.编辑系统变量文件：vi /etc/profile

```shell
export JAVA_HOME=/usr/java/jdk1.8.0_151
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib
export PATH=$JAVA_HOME/bin:$PATH
```

## 3.进程

```shell
netstat -tunlp |grep 22 //查看22端口使用情况
```

## 4.tar解、压缩

1.**tar**

```shell
-c: 建立压缩档案
-x：解压
-t：查看内容
-r：向压缩归档文件末尾追加文件
-u：更新原压缩包中的文件
```

2.这五个是独立的命令，压缩解压都要用到其中一个，可以和别的命令连用但只能用其中一个。下面的参数是根据需要在压缩或解压档案时可选的。

```shell
-z：有gzip属性的
-j：有bz2属性的
-Z：有compress属性的
-v：显示所有过程
-O：将文件解开到标准输出
```

3.下面的参数-f是必须的

```shell
# -f: 使用档案名字，切记，这个参数是最后一个参数，后面只能接档案名。
# 这条命令是将所有.jpg的文件打成一个名为all.tar的包。-c是表示产生新的包，-f指定包的文件名。
tar -cf all.tar *.jpg
# 这条命令是将所有.gif的文件增加到all.tar的包里面去。-r是表示增加文件的意思。
tar -rf all.tar *.gif
# 这条命令是更新原来tar包all.tar中logo.gif文件，-u是表示更新文件的意思。
tar -uf all.tar logo.gif
# 这条命令是列出all.tar包中所有文件，-t是列出文件的意思
tar -tf all.tar
# 这条命令是解出all.tar包中所有文件，-x是解开的意思
tar -xf all.tar
```

4.压缩

```shell
tar -cvf jpg.tar *.jpg //将目录里所有jpg文件打包成tar.jpg 
tar -czf jpg.tar.gz *.jpg   //将目录里所有jpg文件打包成jpg.tar后，并且将其用gzip压缩，生成一个gzip压缩过的包，命名为jpg.tar.gz
tar -cjf jpg.tar.bz2 *.jpg //将目录里所有jpg文件打包成jpg.tar后，并且将其用bzip2压缩，生成一个bzip2压缩过的包，命名为jpg.tar.bz2
tar -cZf jpg.tar.Z *.jpg   //将目录里所有jpg文件打包成jpg.tar后，并且将其用compress压缩，生成一个umcompress压缩过的包，命名为jpg.tar.Z
rar a jpg.rar *.jpg //rar格式的压缩，需要先下载rar for linux
zip jpg.zip *.jpg //zip格式的压缩，需要先下载zip for linux
```

5.解压

```shell
tar -xvf file.tar //解压 tar包
tar -xzvf file.tar.gz //解压tar.gz
tar -xjvf file.tar.bz2   //解压 tar.bz2
tar -xZvf file.tar.Z   //解压tar.Z
unrar e file.rar //解压rar
unzip file.zip //解压zip
```

6.总结

```sh
1、*.tar 用 tar -xvf 解压
2、*.gz 用 gzip -d或者gunzip 解压
3、*.tar.gz和*.tgz 用 tar -xzf 解压
4、*.bz2 用 bzip2 -d或者用bunzip2 解压
5、*.tar.bz2用tar -xjf 解压
6、*.Z 用 uncompress 解压
7、*.tar.Z 用tar -xZf 解压
8、*.rar 用 unrar e解压
9、*.zip 用 unzip 解压
```
## 5. linux下动态查看日志

```sh
tail -f fileName
```

## 6. 服务自启动

```shell
[root@localhost ~]chkconfig smb on
#or
[root@localhost ~]# vi /etc/rc.local
#!/bin/sh
#
# This script will be executed *after* all the other init scripts.
# You can put your own initialization stuff in here if you don't
# want to do the full Sys V style init stuff.
service smb start
```

## 7.网卡配置

```shell
ifdown eth3 && ifup eth3
service network restart
```

## 8.vi编辑器の操作

```shell
a   //在当前字符后添加文本
A   //在行末添加文本
i   //在当前字符前插入文本
I   //在当前行首插入文本
o   //在当前行后面插入一空行
O   //在当前行前面插入一空行
/字符串 //往下查找字符串
		n   //往下跳到下一个匹配位置
		N   //往上跳到下一个匹配位置
?字符串 //往上匹配字符串（与'/字符串'操作相反）
p   //粘贴文本操作，用于将缓存区的内容粘贴到当前光标所在位置的下方
P   //粘贴文本操作，用于将缓存区的内容粘贴到当前光标所在位置的上方
Ctrl+u  //向文件首翻半屏
Ctrl+d  //向文件尾翻半屏
Ctrl+f  //向文件尾翻一屏
Ctrl+b  //向文件首翻一屏；
Esc     //从编辑模式切换到命令模式；
ZZ      //命令模式下保存当前文件所做的修改后退出vi；
:行号   //光标跳转到指定行的行首；
:$      //光标跳转到最后一行的行首；
x或X    //删除一个字符，x删除光标后的，而X删除光标前的；
D       //删除从当前光标到光标所在行尾的全部字符；
dd      //删除光标行正行内容；
ndd     //删除当前行及其后n-1行；
nyy     //当前行及其下n行的内容保存到寄存器？中，其中？为一个字母，n为一个数字；
gg      //让光标移到首行，在vim才有效，vi中无效
G       //光标移到最后一行
V       //是进入Visual(可视）模式
dG      //全部删除
ggyG    //全部复制
ggVG    //全选
		d       //删除选中
		y       //复制选中内容到0号寄存器
"+y     //复制选中内容到＋寄存器，也就是系统的剪贴板，供其他程序用
gg"+yG  //要复制到别的地方，用 "+y 来复制
```

## 9.时间戳

```
date    //获取当前系统时间
date -d '@1526973325'   //将时间戳转换为时间
```

## 10.文件比较之'diff'

## 11. linux 从一个IP机器跳转至另一IP机器: ssh
ssh – OpenSSH remote login client
```shell
$ man ssh
	NAME
	     ssh – OpenSSH remote login client
	
	SYNOPSIS
	     ssh [-46AaCfGgKkMNnqsTtVvXxYy] [-B bind_interface] [-b bind_address] [-c cipher_spec] [-D [bind_address:]port] [-E log_file] [-e escape_char] [-F configfile] [-I pkcs11] [-i identity_file] [-J destination]
	         [-L address] [-l login_name] [-m mac_spec] [-O ctl_cmd] [-o option] [-p port] [-Q query_option] [-R address] [-S ctl_path] [-W host:port] [-w local_tun[:remote_tun]] destination [command [argument ...]]
# 示例
ssh username@IP
password：
```
    
## 12. linux系统间的文件（目录）传输

- 命令：scp 选项 参数
```shell
scp "文件名"  "用户名@计算机IP或者计算机名称:远程路径"      //将本地文件拷贝到远程
scp -r "目录名"  "用户名@计算机IP或者计算机名称:远程路径"   //将本地目录拷贝到远程
scp "用户名@计算机IP或者计算机名称:文件名"  "本地路径"      //从远程将文件拷回本地
scp -r "用户名@计算机IP或者计算机名称:目录名"  "本地路径"   //从远程将目录拷回本地
    -a	尽可能将档案状态、权限等资料都照原状予以复制
    -r	若 source 中含有目录名，则将目录下之档案亦皆依序拷贝至目的地
    -f	若目的地已经有相同档名的档案存在，则在复制前先予以删除再行复制
    -v	和大多数 linux 命令中的 -v 意思一样 , 用来显示进度 . 可以用来查看连接 , 认证 , 或是配置错误
    -C	使能压缩选项
    -P	选择端口 . 注意 -p 已经被 rcp 使用
    -4	强行使用 IPV4 地址
    -6	强行使用 IPV6 地址
    ```
    

## 13. linux centos7 关于防火墙操作
    
```shell
# 在root权限下查看防火墙状态。 
systemctl status firewalld

# 临时关闭防火墙命令。重启电脑后，防火墙自动起来。 
systemctl stop firewalld

# 永久关闭防火墙命令。重启后，防火墙不会自动启动。 
systemctl disable firewalld

# 打开防火墙命令。 
systemctl enable firewalld
```
