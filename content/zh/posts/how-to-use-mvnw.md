---
title: "如何使用mvnw"
date: 2021-01-24T17:25:56+08:00
draft: false
---

环境：
- Windows10
- JDK8
- SpringBoot
- Idea社区版

# 前言
mvnw即Maven Wrapper，该工具可以在大型、多人协作的项目统一Maven版本，避免因为版本不一致造成意想不到的bug

# 如何安装
在项目的根目录运行控制台，输入命令
```
mvn -N io.takari:maven:wrapper
```

# 如何使用
安装完之后跟mvn命令一样的使用方法，不同的是要将  mvn 改成 mvnw.cmd
```
mvnw.cmd clean package（第一次用mvnw.cmd需要等待下载指定版本的Maven，需要耐心等待）

// 如果是Linux/MacOS
mvnw clean package
```

# 关于Maven的配置
settings.xml
放在当前用户的.m2目录下，自动读取