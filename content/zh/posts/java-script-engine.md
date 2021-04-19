---
title: "Java脚本引擎"
date: 2021-04-19T10:30:42+08:00
draft: false
---

# 什么是Java脚本引擎？
指的是将脚本嵌入Java代码，实现轻量级、灵活的业务逻辑，具体可以应用于流程、规则

# JDK各版本的脚本引擎
- JDK6 用的是Rhnio，仅解释模式
- JDK8 开始用的是Nashorn引擎，仅编译模式。将javascript编译成字节码，交给jvm执行

> Nashorn引擎在JDK15被彻底移除，之后不会在JDK中提供脚本引擎支持，不过我们还是可以通过第三方库的方式继续使用Nashorn，或者使用性能更好的替代品GraalVM javascript

# Nashorn是什么
既然JDK8版本将Rhnio改为Nashorn了，那么我们把关注点焦点放到Nashorn。
它是一个完全支持ECMA5.1规范的js引擎，被内嵌到java中,能将js代码编译成java字节码，使得java能够执行js脚本。

# Nashorn怎么用

<div class='mermaid'>
graph TD
A[开始] --> B(获取脚本引擎)
B --> C(编译脚本)
C --> D(执行)
D --> F(结束)
</div>

有两种使用方式：

1、直接调用scriptEngine的eval()方法，内部会编译再执行

2、先编译成comiledScript  再执行eval()，仅编译一次，经过验证这种方式执行效率高，特别是执行次数多的时候

示例代码：
```java
    ScriptEngineManager manager = new ScriptEngineManager();
    ScriptEngine engine = manager.getEngineByName("js");
    engine.eval("print('hello')");
```

# 其他脚本语言的支持
Nashorn引擎默认仅支持js，我们可以通过拓展、聚合的方式支持更多的脚本语言

|     语言    |    实现方案  |  备注       |
| ----------- | ----------- | ----------- |
| js          | Javax.script |Nashorn引擎 |
| Groovy      | 继承javax.script AbstractScriptEngine        |Nashorn引擎+依赖提供groovy|
| Spel          | 不依赖javax.script |通过依赖提供 |
| ognl          | 不依赖javax.script |通过依赖提供 |
| java          | 创建临时源代码，动态编译，反射创建执行 | |