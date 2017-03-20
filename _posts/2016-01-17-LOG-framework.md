---
layout: post
title: LOG-framework
category: Python
description: java 日志框架， 简单备忘。
---

总结一下Java中接触到的日志组件的用法，上code

### SLF4J
这个组件的好处就是可以用{}进行拼接日志语句，不用自己ugly的去拼写

```Java  
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
// 需要引入两个package slf4j-api 以及 slf4j-simple
public class logAPP {

	public static void main(String[] args) {
	    Logger logger = LoggerFactory.getLogger(logAPP.class);
	    int a = 55;
	    System.out.println(logger.isDebugEnabled());
	    logger.debug("Hello World, the number is {}",a);
	    logger.info("Hello World, the number is {}",a);
	    logger.error("Hello World");
	    logger.warn("Hello World");
	}
  // 这样一来就可以打印了，不过slf4j-simple的缺点就是只能打印到info一级的信息，并且只能打印到system.error
}

```

为了实现打印debug的信息，以及可以定向到文件，可以让SLF4J搭配其他组件进行配合，常用的有Log4j，JDK自带的logging，logback

### Log4j
Log4j是广泛使用的组件，支持各种级别的日志，和输出到文件，在适配上文的代码的过程中不需要在代码上做任何修改  
1. 添加配置文件，如果是标准的mvn文件就放在**src/main/resources**里面，如果只有一个src文件夹，就直接放在src下面，不要嵌套任何package，准确的说法是Java会从classpath里面去找这个文件，如果不知classpath怎么找可以这样。  
2. 部分配置文件  
3. 修改pom.xml，添加slf4j-log4j12 和 log4j-api 和 log4j-core  

```java
ClassLoader cl = ClassLoader.getSystemClassLoader();

URL[] urls = ((URLClassLoader)cl).getURLs();

for(URL url: urls){
	System.out.println(url.getFile());
}
```

```
# Root logger option
log4j.rootLogger=DEBUG, stdout, file

# Redirect log messages to console
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.Target=System.out
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n

# Redirect log messages to a log file, support file rolling.
log4j.appender.file=org.apache.log4j.RollingFileAppender

log4j.appender.file.File=C:\\log4j-application.log
#Redirect to Tomcat logs folder,可以使用环境变量，或在文件中定义catalina.home=xxx
#log4j.appender.file.File=${catalina.home}/logs/logging.log

log4j.appender.file.MaxFileSize=5MB
log4j.appender.file.MaxBackupIndex=10
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n

// 设置特定的class的level 这个很重要
log4j.logger.my.package.class=info
log4j.logger.my.package=info
```
至此，log4j的需求基本满足，接下来看看logging

### jdk-logging
jdk自带了一种日志工具叫logging，但是用的并不多。logging用的level和其他的组件不是一个序列，而是SEVERE,WARNING,INFO,FINE...也支持定向到文件和设置特殊的级别。缺点也是需要自己去拼接字符串。  
下面结合slf4j  
1. 代码不变，由于logging是jdk自带，所以pom中有且只有slf4j-api，slf4j-jdk14  
2. 写配置文件  
3. 设置环境变量，如果是命令行可以是-Djava.util.logging.config.file=xxxx，或者是在代码中System.setProperty(key,value),注意必须是绝对路径  

```
handlers=java.util.logging.FileHandler, java.util.logging.ConsoleHandler
java.util.logging.ConsoleHandler.level=FINE
java.util.logging.ConsoleHandler.formatter=java.util.logging.SimpleFormatter

java.util.logging.FileHandler.level=FINE
java.util.logging.FileHandler.pattern=xxxxlogging

# Write 10MB before rotating this file
java.util.logging.FileHandler.limit=10000000

# Number of rotating files to be used
java.util.logging.FileHandler.count=4
java.util.logging.FileHandler.formatter=java.util.logging.SimpleFormatter

.level=FINE
## 某个package的设置
my.package.level=FINE
my.package.class.level = INFO
```

### logback
相比如jdk中的logging，这个用的更加多。使用logback的方法如下：  
1. 代码不变  
2. 添加依赖 logback-classic  
3. 将配置文件加入到classpath中去  

```
<configuration>
  <appender name="FILE" class="ch.qos.logback.core.FileAppender">
    <file>myApp.log</file>
    <encoder>
      <pattern>%date %level [%thread] %logger{10} [%file:%line] %msg%n</pattern>
    </encoder>
  </appender>
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%msg%n</pattern>
    </encoder>
  </appender>
  <!--设置不同层次的level，这里需要手动确认自己填的类型是否符合，大小写敏感 -->
  <logger name="my.package" level = "DEBUG"/>
  <logger name="my.package.class" level = "DEBUG"/>

  <root level="INFO">
    <appender-ref ref="FILE" />
    <appender-ref ref="STDOUT" />
  </root>
</configuration>
```

如果同时加入了多个log的框架，那么在console中会打出相应的warning并根据vm中选出一个。解决方案是根据打印出来的信息将多余的package进行exclusive  
总的来说slf4j-api是必须的，然后其他的类似于一个适配器，让slf4j与其他组件兼容，把优雅的message打印出来。
更多搭配就在[SLF4J官网](http://www.slf4j.org/manual.html)
