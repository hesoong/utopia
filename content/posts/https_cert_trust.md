---
title: 报错 | PKIX path building failed
date: 2024-12-23T14:40:18+08:00
draft: false
toc: false
images: 
tags:
  - java
---
> 我们在开发过程中经常会遇到https 请求失败的问题：PKIX path building failed，这往往是由于网站使用自签证书，不是官方证书授权机构授权的证书，在请求加密过程中证书验证失败导致。详细如下报错：

```log
Caused by: javax.net.ssl.SSLHandshakeException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
	at java.base/sun.security.ssl.Alert.createSSLException(Alert.java:131)
	at java.base/sun.security.ssl.TransportContext.fatal(TransportContext.java:369)
	at java.base/sun.security.ssl.TransportContext.fatal(TransportContext.java:312)
	at java.base/sun.security.ssl.TransportContext.fatal(TransportContext.java:307)
...
```

那么接下来我们来总结下解决这个问题的几个方法：
首先得下载证书 参考如下图
![](export_cert.png)
### 1. 将证书添加到本地JVM的信任存储库

```shell
keytool -import -trustcacerts -keystore "%JAVA_HOME%\jre\lib\security\cacerts" -storepass changeit -alias "your alias" -import "path_to_cert_file" 
```
导入成功后重启应用即可。
### 2. 使用自定义信任库

可以将证书导入到一个自定义的证书库（参考上面，keystore后面指定你自定义路径），然后通过代码指定你的信任库文件

```java
System.setProperty("javax.net.ssl.trustStore", "/path/to/cert/cacerts");  
System.setProperty("javax.net.ssl.trustStorePassword", "changeit");
```
或者是通过程序启动JVM参数

```shell
-Djavax.net.ssl.trustStore # 指定 TrustStore 文件的路径。
-Djavax.net.ssl.trustStorePassword # 指定 TrustStore 文件的密码。
-Djavax.net.ssl.trustStoreType #指定 TrustStore 文件的类型，默认是 `JKS`。其他常见类型包括 `PKCS12`
```

还有一种，如果项目使用SpringBoot， 可以通过在配置文件使用`server.ssl.trust-store` `server.ssl.trust-store-password` 来指定，但是我没有成功。