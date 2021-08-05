---
title: "Servlet"
date: 2021-08-03T15:37:54+08:00
draft: false
tags: ["java"]
categories: ["java"]
---

## Servlet相关知识

**==Servlet只是接口，是Java中的一个接口文件；==**

#### 有什么用？

处理请求数据，进行逻辑处理和数据操作；**相比之下，对于Servlet的疑惑，实际上是对Tomcat的不了解！**

就上方所说，Servlet仅仅是一个接口，其功能是提供一个网络交互的功能，里面写好了对应的接口，然而实际上起到接受请求和发送请求的是Tomcat。Tomcat将接受到的网络请求封装为对象，传递给对应的Servlet的对象；同样，Servlet所发送出去的请求也是由Tomcat发送出去的；

而Servlet并不是没有任何用处，他将各种功能封装成指定的方法方便上层调用。可以从Servlet的源代码中看出，并没有相关处理接受网络请求以及发送网络请求的任何代码；

> ```Java
> // Servlet接口中的注释说明：
> * Called by the servlet container to indicate to a servlet that the servlet
> * is being placed into service.
> ```

可以看出， servlet是被container所调用的，而这里的container就是所说的Tomcat程序；

再来servlet的方法：

![image-20210803165413849](https://gitee.com/ymyguang/picture/raw/master/img/image-20210803165415584.png)

- init ：container调用当前servlet时，首先执行该方法，开始初始化当前servlet
- getServletConfig() ：获取配置内容
- service() ：执行逻辑代码
- getServletInfo()：获取当前Servlet的信息
- destory()：销毁当前Servlet

**从接口的方法也可以看出，servlet仅仅是一个类，仅仅是为了规范代码以方便Tomcat调用执行相关操作！**



也可以说：Tomcat程序是按照Servlet API的接口实现的， 而Tomcat的功能就是将Java类转换成实际响应的程序，以此如果希望得到web程序（可以接受请求，发送响应），就必须满足Servlet API规范，也就是继承Servlet相关类，继承之后也即是广义上所说的Servler！

下图是大体上的请求客户端和服务器的交互流程

![image-20210803171359420](https://gitee.com/ymyguang/picture/raw/master/img/image-20210803171359420.png)

### 类中的一些关键方法

this.servletContext();

用于不同的Servlet之间传递属性值，值得注意的是，多个不同的Servlet取值时，都是this.来获取的。