---
title: "Keytool 命令"
date: 2023-08-21T09:55:51+08:00
draft: true
toc: false
images:
tags:
  - 命令
---

## 介绍
---
`Keytool` 是一个Java 数字证书的管理工具， `Keytool`将密钥（key）和证书（certificates）存在一个称为keystore的文件中 在keystore里，包含两种数据：
1. 密钥实体（Key entity）——密钥（secret key）又或者是私钥和配对公钥（采用非对称加密）
2. 可信任的证书实体（trusted certificate entries）——只包含公钥

## 命令
```shell
✗ keytool -h                                                                                                                       
Key and Certificate Management Tool

Commands:

 -certreq            Generates a certificate request
 -changealias        Changes an entry's alias
 -delete             Deletes an entry
 -exportcert         Exports certificate
 -genkeypair         Generates a key pair
 -genseckey          Generates a secret key
 -gencert            Generates certificate from a certificate request
 -importcert         Imports a certificate or a certificate chain
 -importpass         Imports a password
 -importkeystore     Imports one or all entries from another keystore
 -keypasswd          Changes the key password of an entry
 -list               Lists entries in a keystore
 -printcert          Prints the content of a certificate
 -printcertreq       Prints the content of a certificate request
 -printcrl           Prints the content of a CRL file
 -storepasswd        Changes the store password of a keystore

Use "keytool -?, -h, or --help" for this help message
Use "keytool -command_name --help" for usage of command_name.
Use the -conf <url> option to specify a pre-configured options file.
```

### `keytool -list`
```shell
✗ keytool -list -h
keytool -list [OPTION]...

Lists entries in a keystore

Options:

 -rfc                    output in RFC style
 -alias <alias>          alias name of the entry to process
 -keystore <keystore>    keystore name
 -cacerts                access the cacerts keystore
 -storepass <arg>        keystore password
 -storetype <type>       keystore type
 -providername <name>    provider name
 -addprovider <name>     add security provider by name (e.g. SunPKCS11)
   [-providerarg <arg>]    configure argument for -addprovider
 -providerclass <class>  add security provider by fully-qualified class name
   [-providerarg <arg>]    configure argument for -providerclass
 -providerpath <list>    provider classpath
 -v                      verbose output
 -protected              password through protected mechanism

```