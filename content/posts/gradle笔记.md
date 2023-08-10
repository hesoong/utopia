---
title: "Gradle笔记"
date: 2018-08-26T17:10:50+08:00
draft: false
toc: true
images:
tags:
  - 笔记
---

## gradle 国内镜像配置

### 单项目

对单个项目生效，在项目中的build.gradle修改内容

```groovy
buildscript {
    repositories {
        maven { url 'http://maven.aliyun.com/nexus/content/groups/public/' }
                maven{ url 'http://maven.aliyun.com/nexus/content/repositories/jcenter'}
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.2.3'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }        
}

allprojects {
    repositories {
        maven { url 'http://maven.aliyun.com/nexus/content/groups/public/' }
        maven{ url 'http://maven.aliyun.com/nexus/content/repositories/jcenter'}
    }
}
```

### 全局

对所有项目生效，在`${USER_HOME}/.gradle/`下创建`init.gradle`文件

```groovy
allprojects{
    repositories {
        def ALIYUN_REPOSITORY_URL = 'http://maven.aliyun.com/nexus/content/groups/public'
        def ALIYUN_JCENTER_URL = 'http://maven.aliyun.com/nexus/content/repositories/jcenter'
        all { ArtifactRepository repo ->
            if(repo instanceof MavenArtifactRepository){
                def url = repo.url.toString()
                if (url.startsWith('https://repo1.maven.org/maven2')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_REPOSITORY_URL."
                    remove repo
                }
                if (url.startsWith('https://jcenter.bintray.com/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_JCENTER_URL."
                    remove repo
                }
            }
        }
        maven {
            url ALIYUN_REPOSITORY_URL
            url ALIYUN_JCENTER_URL
        }
    }
}
```

## 相关命令

### gradle命令查看目录树

如果存在依赖冲突，在默认情况下，Gradle会选择最新版本，这和Maven是不同的，Maven会选择离依赖树最近的版本。当然，我们可以通过设置`Configuration`的`resolutionStrategy`来重新设置依赖冲突的处理规则，对此本文将不予讲解。

```shell
# gradle 查看目录树
gradle dependencies
# gradle 输出目录树到text.txt文件
gradle dependencies > text.txt
#gradlew 查看目录树输出到文件
gradlew -q :dependencies > text.txt
```

## gradle脚本

### 变量要用双引号

如：version: "$springBootVersion"

```groovy
ext {
  springBootVersion = "2.0.4.RELEASE"
  fastJsonVersion = "1.2.35"
  druidVersion = "1.0.26"
  bootRedisVersion = "2.0.4.RELEASE"
}

dependencies {
    //compile("org.springframework.boot:spring-boot-starter-web")
    //testCompile('org.springframework.boot:spring-boot-starter-test')
    testCompile group: 'org.springframework.boot', name: 'spring-boot-starter-test', version: "$springBootVersion"
    compile group: 'org.springframework.boot', name: 'spring-boot-starter-web', version: "$springBootVersion"
    // This dependency is exported to consumers, that is to say found on their compile classpath.
    api 'org.apache.commons:commons-math3:3.6.1'

    // This dependency is used internally, and not exposed to consumers on their own compile classpath.
    implementation 'com.google.guava:guava:23.0'

    // Use JUnit test framework
    //testImplementation 'junit:junit:4.12'
    testCompile group: 'junit', name: 'junit', version: '4.12'
    
    compile group: 'org.postgresql', name: 'postgresql', version: '9.4.1212'
 compile("org.springframework.boot:spring-boot-starter-data-jpa:$springBootVersion"){
    exclude (module: "xml-apis")
    }
    compile group: 'org.projectlombok', name: 'lombok', version: '1.16.20'
}
```
