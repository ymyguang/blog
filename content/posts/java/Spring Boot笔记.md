---
title: ""
date: 2021-07-28T17:24:47+08:00
draft: false
tags: ["","",""]
categories: ["","",""]
---

### Spring Boot笔记

### 快速开始

##### **核心启动类**：(the entry point ),Spring 容器启动的入口点；

```java
@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

interface：**ApplicationRunner**,在Spring启动后，执行；

```java
public void run(ApplicationArguments arg0) throws Exception {
	System.out.println("Hello World from Application Runner");
}
```

##### 读取配置文件：application.yml

Spring 会自动读取ClassPath下的该文件，取值方法如下：

```java
@Value("${spring.application.name}")
private String name;
```

当前方式只支持在注解中使用，同时为了避免当读取字段不存在时，导致Spring抛出**Could not resolve placeholder 'spring.application.name' in value "${spring.application.name}"**.异常，因此可以在朝朝属性时，在属性的背后加上默认值；@Value("${spring.application.name:default}")

在实际的开发过程中，可能会面临不同的环境，因此不可避免地需要配置多个配置问价供不同的配置文件使用；此种情况有两种方式：

方式一：分离application.properties文件，不同的配置环境对应到不同的配置名称；

​		dev:application-dev.properties

​		prod:application-prod.properties

使用不同的配置文件方式：java -jar 需要运行的jar文件包 -spring.profiles.active=dev，这里需要注意的是，等号后面的值是：application-xxx.properties中的xx得的值；

**方式二：**

​	使用application.yml文件配置，相比于方式一，此种方式更加简洁，配置中的结构更加清晰，另外不同的环境也不去需要分离出单独的文件，只需要—即可在文件中实现分割；但是需要指定配置文件的ID名称：对应字段：profiles:XXXX

使用方式同方式一，直接使用-spring.profiles.active=xxx指定配置名称；



##### 输出日志

```yml
logging:
  file:
    name: log.txt
```

**The code given below shows how to add the slf4j logger in Spring Boot main class file.**

```java
package com.tutorialspoint.demo;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {
   private static final Logger logger = LoggerFactory.getLogger(DemoApplication.class);
   
   public static void main(String[] args) {
      logger.info("this is a info message");
      logger.warn("this is a warn message");
      logger.error("this is a error message");
      SpringApplication.run(DemoApplication.class, args);
   }
}
```

