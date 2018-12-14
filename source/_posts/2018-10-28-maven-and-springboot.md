---
layout: post
title: "Maven 和 SpringBoot"
category: "java"
tags: ["maven", "spring"]
---

## Maven 常用依赖

### dependencies vs dependencyManagement

* **dependencies**：引入依赖。子项目会从父项目中继承该依赖项
* **dependencyManagement**：声明依赖，并不实现引入。因此子项目需要显示的声明需要用的依赖。如果不在子项目中声明依赖，是不会从父项目中继承下来的；只有在子项目中写了该依赖项，并且没有指定具体版本，才会从父项目中继承该项，并且 version，scope 都读取自父pom；另外如果子项目中指定了版本号，那么会使用子项目中指定的jar版本

### spring-boot-starter-parent

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.10.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

spring-boot-starter-parent 是一个特殊的 starter，它用来提供相关的Maven默认依赖，使用它之后，常用的包依赖可以省去version标签。可查看 `.m2\repository\org\springframework\boot\spring-boot-dependencies\1.5.10.RELEASE\spring-boot-dependencies-1.5.10.RELEASE.pom` 

### [maven-surefire-plugin](https://maven.apache.org/surefire/maven-surefire-plugin/)

> The Surefire Plugin is used during the test phase of the build lifecycle to execute the unit tests of an application. It generates reports in two different file formats Plain text files (.txt) XML files (.xml)

### maven-resources-plugin
## 参考

1. [Spring Boot干货系列：（一）优雅的入门篇](http://tengj.top/2017/02/26/springboot1/)