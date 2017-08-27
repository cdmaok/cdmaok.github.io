---
layout: post
title: Maven Plugins
category: Java,Maven
description: 一些简单的maven插件介绍
---

### maven-compiler-plugin
  * 最基本的plugin，可以指定编译的jdk版本等

### maven-antrun-plugin
  * 用于运行ant任务，ant可以完成灵活度更高的任务  

### maven-archetype-plugin
  * mvn archetype:generate 用以生成骨架，生成src/main, src/test等文件夹
  * 可以继续定义骨架，帮助用户快速的二次开发

### maven-assembly-plugin
  * 制作项目分发包，生成jar，zip，war等文件，需要配置一个assembly-plugin
  * mvn assembly: single 在pom中指定descriptorRefs，包括bin, jar-with-dependencies, src, and project

### maven-dependency-plugin
  * 用来管理依赖问题
  * mvn dependency:copy 可以将项目依赖从m2库中拷出某个制定文件夹中
  * mvn dependency:analyze 分析used undeclared dependency & unused declared dependency
  * mvn dependency:analyze-duplicate 对依赖进行去重
  * mvn dependency:go-offline 将所有依赖下至线下

### maven-enforcer-plugin
  * 在父模块中制定规则，如依赖的版本，禁止某些依赖等，如果子pom违反了这些规则将报错

### maven-help-plugin
  * mvn help:effective-pom & mvn help:effective-settings 打印需要的项目，检查pom的配置是否正确

### maven-release-plugin
  * 在scm上帮助发布版本

### maven-resources-plugin
  * mvn resources:copy-resources copy resources to certain directory.

### maven-surefire-plugin
  * test 插件，可以用来生成test报告等
  * 指定特定的suite等
  * mvn surefire：text

### build-helper-maven-plugin
  * 添加更多的源码文件夹如src/main/scala等操作



Note: 来自[InfoQ](http://www.infoq.com/cn/news/2011/04/xxb-maven-7-plugin)
