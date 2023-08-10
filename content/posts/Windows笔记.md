---
title: windows相关
date: 2017-10-31
updated: 2018-10-31
toc: true
tags:
- 笔记
---
## Windows关闭/开启管理员用户

```bash
# 管理员权限进入cmd
net user administrator /active:tes // 开启
net user administrator /active:no // 关闭
```

## Windows批处理（.bat）

### .bat示例

```bash
@echo off
rem 准备运行uxdb
cd "D:\Program Files\UXDB\dbsql\bin" 
d:
start uxdbStart.bat
rem 准备运行redis
cd "C:\Program Files\Redis\" 
c:
start redisStart.bat
rem 准备运行activemq
cd C:
cd C:\Users\bms\Desktop\apache\apache-activemq-5.10.0\bin\ 
start activeMQStart.bat
```

### Note

1. 切换盘符：

```bash
# 同一盘符可以随意切换
C:\Users\bms>cd "C:\Program Files\Redis"  #若当前盘符中有空格，需要将盘符路径用“”引起来，否则会出错
# 不同盘符的切换（比如当前在C盘）
C:\Users\bms>cd "D:\Program Files\UXDB\dbsql\bin"
    
C:\Users\bms>       #盘符并没有切换

C:\Users\bms>d：    #此时再输入要切换的盘符名并以：结束

D:\Program Files\DB\dbsql\bin>    #成功切换到目标目录
```

2. 批处理中启动另外批处理文件

- call ：启动一个成功或手动关闭后才会执行下一个bat文件
- start：不会发生阻塞，并行启动

## Windows配置服务（命令行）

SC 是用于与服务控制管理器通信的命令行程序。
用法:

```bash
sc <server> [command] [service name] <option1> <option2>...

选项 <server> 的格式为
可以键入 "sc [command]"以获得命令的进一步帮助
命令:
    query---------------查询服务的状态，或枚举服务类型的状态。
    queryex------------查询服务的扩展状态， 或枚举服务类型的状态。
    start----------------启动服务。
     pause--------------发送 PAUSE 控制请求到服务。
    interrogate--------发送 INTERROGATE 控制请求到服务。
    continue-----------发送 CONTINUE 控制请求到服务。
    stop----------------发送 STOP 请求到服务。
    config--------------(永久地)更改服务的配置。
    description--------更改服务的描述。
    failure--------------更改服务失败时所进行的操作。
    qc-------------------查询服务的配置信息。
    qdescription-------查询服务的描述。
    qfailure-------------查询失败服务所进行的操作。
    delete---------------(从注册表)删除服务。
    create---------------创建服务(将其添加到注册表)。
    control--------------发送控制到服务。
    sdshow--------------显示服务的安全描述符。
    sdset----------------设置服务的安全描述符。
    GetDisplayName---获取服务的 DisplayName。
    GetKeyName--------获取服务的 ServiceKeyName。
    EnumDepend-------枚举服务的依存关系。

下列命令不查询服务名称:
sc <server> <command> <option>
    boot---------------(ok | bad) 表明是否将上一次启动保存为最后所知的好的启动配置
    Lock---------------锁定服务数据库
    QueryLock-------查询 SCManager 数据库的 LockStatus
```

### 使用

在出现的命令行窗口(**开始->运行->cmd**)中输入sc
使用`sc/?`命令，查询其它功能
示例 1:注册服务(类似与linux中系统启动自动加载的东西)

```bash
sc create SVN binpath= "C:\Program Files\Subversion\bin\svnserve.exe --service -r D:\svn" displayname= "Subversion Server" depend= Tcpip start= auto
    其中sc create是sc注册服务命令
    svn : 是服务注册时的键名
    binpath : 是服务加载程序启动文件的路径和命令参数
    displayname ：是服务显示名
    depend      ：传输依赖的协议
    start       ：是否自动启动
    
    说明：上面的那个例子是注册一个svn的服务
```

示例 2:启动服务

```bash
sc start MyService
```

## windows 相关操作

### 1. 远程桌面

```bash
mstsc
```

### 2. 查看服务

```bash
win+R-->services.msc
```

### 3. 查看端口占用

```bash
netstat -ano | findstr 10086
```
