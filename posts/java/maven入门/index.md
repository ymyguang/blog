# Maven入门


## Maven 笔记

#### 一、Maven是什么？

​	Maven 是Java中自动构建代码和解决依赖的工具。主要有POM文件提供配置参数；有了Maven之后，可以快速的创建，编译、测试等功能，极大的提高了项目的调试速度；其中关键在于POM文件的内容配置，POM是一个以xml结尾的文件。内容结构类似于HTML的标签，有开始和结束的标签，属性相关内容写入到开始标签中。

以下是一个POM文件内容：

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.javatpoint</groupId>
  <artifactId>CubeGenerator</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>CubeGenerator</name> 
  <url>http://maven.apache.org</url>
  <properties>
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
  </properties>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```

简单的标签作用说明：

- <groupID> : 组织的ID，例如以上的文件内容：com.javatpoint；
- <artifactID> 组织内的ID,此标签中的内容和上方的额<groupID>中所存在的内容的拼接，能直接在在从仓库中定位到；
- <version>声明版本号，用于区分groupID和artifactID重名的工程

以上的POM问文件的完整形式实际上是继承了父类的POM文件

```shell
# 查看完整POM内容方式
mvn help:effective-pom
```

### 二、Maven的简单使用

#### 2.1. 编译项目文件

```shell
mvn clean compile    # 清空之前编译的文件后，开始编译
```



