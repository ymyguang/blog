---
title: "tomcat乱码"
draft: false
date: 2020-06-29T15:05:22+08:00
tags: ["coding", "Java","problem","踩坑"]
categories: ["coding","problem"]
---

### 记一次tomcat乱码的解决方法



起初在是使用tomcat的时候，一脸懵逼不是到是什么。实际上tomcat是由Java编写的服务器软件。

之前在IDEA里面，使用tomcat，但是软件中的控制台中，显示乱码。想了想，怀疑是编码不对称。



在cmd中输入：chcp ，显示936，查询可知，该代码是GBK2312的编码，于是乎打算更改成65001（UTF-8）的编码格式，但是修改的作用域仅仅在当前窗口的cmd，关闭之后还是恢复成默认的936代码

​	尝试更改regedit，将CodePage更改成：65001，但是还是乱码。

百度一阵子，得到了解决方案：

在tomcat下的conf中的：logging.properties文件，把其中的

![image-20201014193008538](https://gitee.com/ymyguang/picture/raw/master/img/image-20201014193008538.png)

等号后面改成GBK2312，再次启动tomcat，就不乱码了。





问题分析：tomcat在执行的过程中，将默认的编码设置成UTF-8，但是cmd显示的是GBK，因此乱码。