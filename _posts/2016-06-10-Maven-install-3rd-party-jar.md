---
layout: post
title: maven-install-3rd-party-jar
category: git
description: 使用maven安装第三方的包的方法小结。
---

### Scenario  
项目里面需要调用一个开源的包（不想重复造轮子），一般的方法就是直接去maven的repo里面找依赖，可是这个开源包并没有加入到repo里面，所以需要用另外的方式解决。


### Solution  

1. 本地安装。  
假设这个包的名字是info.jar.  在本地的工程目录里加入./lib/info.jar，这个时候有三种方法，一种是安装到local repo;一种是添加repo；另外一种是在dependency中指定这个jar的路径。
  第一种：  

```
~~~shell
mvn install:install-file -Dfile=<path-to-file> -DgroupId=<group-id> -DartifactId=<artifact-id> -Dversion=<version> -Dpackaging=jar
## 这样maven会生成对应的jar的pom.xml, 执行这个命令之后可以和其他jar包一样在project的pom里面添加依赖即可。
~~~
```

    第二种：

```
## 添加 repo 指定目录，这种方法是需要info.jar 是带pom文件的。
~~~xml
<repositories>
    <repository>
        <id>project-repo</id>
        <url>file://${project.basedir}/src/main/resources/repo</url>
    </repository>
</repositories>
~~~
```

  第三种：  

```
不添加到local repo里面，而是直接在dependency里面指定路径  
~~~xml
<dependency>
groupId ...
artifactId ...
version ...
<scope>system</scope>
<systemPath>${project.basedir}/src/main/resources/info.jar</systemPath>
</dependency>
~~~
```

三种方法里面顺序靠后的不推荐使用。第一种方法导致部署的时候，在package之前需要mvn install file，如果下游有travis CI 的话，需要在__before_install__的地方加上一个脚本先安装第三方的包  

2. 托管到jitpack  
第二种是比较偷懒的方法，直接用现成的app完成这件事jitpack
在github或者bitbucket上面建一个repo，上面放info.jar的源码还有pom.xml等依赖，并生成一个tag版本，然后直接修改project的pom.xml  

```
~~~
## 添加第三方repo
<repositories>
  <repository>
      <id>jitpack.io</id>
      <url>https://jitpack.io</url>
  </repository>
</repositories>
## 添加依赖
<dependency>
    <groupId>com.github.User</groupId>
    <artifactId>Repo</artifactId>
    <version>Tag</version>
</dependency>
~~~
```  
这样就可以完成第三方的依赖了。
