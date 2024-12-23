---
title: Https_cert_trust
date: 2024-12-23T14:40:18+08:00
draft: false
toc: true
images: 
tags:
  - untagged
---
> 我们在开发工程种经常会遇到https 请求失败的问题，这往往是由于证书验证失败导致。你经常会在控制台看到如下报错：

```log
Caused by: javax.net.ssl.SSLHandshakeException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
	at java.base/sun.security.ssl.Alert.createSSLException(Alert.java:131)
	at java.base/sun.security.ssl.TransportContext.fatal(TransportContext.java:369)
	at java.base/sun.security.ssl.TransportContext.fatal(TransportContext.java:312)
	at java.base/sun.security.ssl.TransportContext.fatal(TransportContext.java:307)
...
```

那么接下来我们来总结下如何解决这种问题。

