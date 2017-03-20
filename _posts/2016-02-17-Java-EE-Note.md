---
layout: post
title: Java-EE-Note
category: Java
---

## Java Spring

### web.xml
1. Servlet  

```
<?xml version="1.0" encoding="UTF-8"?>  
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5">  
  <display-name>s2sh</display-name>  
    <servlet>
        <servlet-name>StudentServlet</servlet-name>
        <servlet-class>
            com.match.servlet.StudentServlet
        </servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>StudentServlet</servlet-name>
        <url-pattern>/StudentServlet</url-pattern>
    </servlet-mapping>
</web-app>  
```

2. Spring  
Spring框架的思路就是在web.xml 中引入contextloader,通过加载applicationContext.xml的内容完成配置  

```
<web-app ...>
  <listener>  
    <listener-class>  
        org.springframework.web.context.ContextLoaderListener
    </listener-class>  
  </listener>   
  <!--contextConfigLocation在ContextLoaderListener类中的默认是/WEB-INF/applicationContext.xml-->  
  <context-param>  
    <param-name>contextConfigLocation</param-name>  
    <param-value>/WEB-INF/applicationContext.xml</param-value>  
    <!-- <param-value>classpath:applicationContext*.xml</param-value> -->  
    </context-param>  
<\web-app>
```

3. Resteasy

```
<web-app xmlns="http://java.sun.com/xml/ns/javaee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
                        http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
                        version="3.0">
    <display-name>Matchp Web Application</display-name>

  	<context-param>
        <param-name>resteasy.servlet.mapping.prefix</param-name>
        <param-value>/api</param-value>
    </context-param>

      <listener>
        <listener-class>org.jboss.resteasy.plugins.server.servlet.ResteasyBootstrap</listener-class>
    </listener>

	<servlet>
        <servlet-name>Resteasy</servlet-name>
        <servlet-class>org.jboss.resteasy.plugins.server.servlet.HttpServletDispatcher</servlet-class>
    </servlet>

    <!-- 这个不是必须的，只是为了使用spring下的applicationContext.xml-->
	  <listener>
        <listener-class>org.jboss.resteasy.plugins.spring.SpringContextLoaderListener</listener-class>
    </listener>
    <!-- 如果不使用spring的标记的话，需要令resteasy 的 autoscan 打开 -->
    <!-- Auto scan REST service -->
	<context-param>
		<param-name>resteasy.scan</param-name>
		<param-value>true</param-value>
	</context-param>

    <servlet-mapping>
        <servlet-name>Resteasy</servlet-name>
        <url-pattern>/api/*</url-pattern>
    </servlet-mapping>

</web-app>
```

### applicationContext.xml
在spring中使用，用于配置bean的各种信息，如果觉得复杂可以直接使用依赖进行注入。  

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:task="http://www.springframework.org/schema/task" xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.1.xsd
        http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-3.1.xsd
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.1.xsd">

    <!-- 使用注解，扫描代码中带注解的bean -->
    <context:component-scan base-package="cn.edu.xmu.gxj.matchp" />
    <!-- used to read some other property -->
    <context:property-placeholder location="xxx"/>
    <!-- 定时操作， 与 @Scheduled 配合使用， 使某些bean定时执行 -->
    <task:annotation-driven />
    <!--  be registered with JAX-RS container to make Jackson the standard JSON reader/writer provider -->
    <bean class="com.fasterxml.jackson.jaxrs.json.JacksonJsonProvider" />

</beans>
```

### spring annotation
有些是spring本身的注解，有些是jsr里面的注解。  

name | meaing
---- | ------
@Repository | 所有带这个标签的都表示为bean
@Component | 也表示bean
@Service | 细分在V
@Controller |  细分为C
@PostConstruct | 在Ioc容器创建bean之后指定执行的方法
@PreDestroy | 销毁前执行的方法
@Autowired | 按变量类型自动装配
@Qualifier | 如果有多个满足要求的bean可以装配，使用这个进行筛选
@Resource | 根据变量名称进行自动装配
@Bean | 也是bean
@Inject | 跟Autowired 一样，在jsr中
@Named | 跟Component 一样，在jsr中
