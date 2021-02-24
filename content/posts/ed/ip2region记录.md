---
title: "IDEA_Java项目的运行Demo"
draft: false
date: 2020-06-29T15:05:22+08:00
tags: ["coding", "Java", "IDEA"]
categories: ["coding","Java"]
---



## IDEA_Java项目的运行Demo

起初是锋锦哥，让我负责流量大师的IP源地址查询任务，自己打算对开源项目进行魔改，于是乎找到了Ip2region的开源项目，打算用Java，但是比较恶心的是，项目就是运行不起来。也可以说在自己没有任何Java构建基础的情况下，尝试去运行一个相比之下，比较复杂的Java项目。

尝试运行的过程中，走了很多弯路。好在最后还是跑起来了，很大的感悟，从刚开始的懵逼，到慢慢的有了一些感性的认识，过程中处处碰壁，好几次都想要放弃，还好我没有。！！！给自己点个赞吧！或许这就是在困难中，才能前进的原因吧，想了想耗时得有4天了，一个简单的项目运行。看样子以后干就完了 。现在想想那些厉害的人，不仅是从技术值得钦佩，在学习的过程中，正是这个遇到问题不逃避，尝试解决的精神，才有大佬这一说！他们懂得多，都是在折腾的过程中，所接触的！

不废话了。下面记录一下心路历程，避免大家少走一些弯路。我尽量 写的详细一点，之所以这次很难跑起来，就是因为百度上的教程太过于简单。

#### 找到对应的源码文件

**编译步骤：源文件—–字节码（class）——机器码**

C语言中 ：源文件 —–预处理——编译——-链接



首先，文件夹内，有三个文件夹，

- 其他的db文件在data中，

- 源码文件在binding中，我这里用的是 Java源码。
- maker是有关Ip2region.db生成的源码文件，暂时不要管他。我们的目的就是把这个项目跑起来，并且可以用到其中的一些功能！

使用Jetbrain IDEA打开该项目文件，可以看到这是打开的界面

这里说一下：三角号。运行

​	这里的运行是需要自己自定义的，在刚开始的时候，没配置的情况下，哪怕是把它点烂，也是没有反应的。

两种运行种类：无论是哪一个，都需要配置一下运行参数。

- 运行整个项目，需要构建

- 运行单个main文件



如下图，在项目还没有进行运行配置的时候，右上角的三角好是黑色的，不能点。因此我们需要进行配置。在这里多少一句，不管软件有多复杂（也就是有多高级）有各种各样的功能，但是自己不要迷了。需要什么用什么就行。剩下的以后会接触到了！



![image-20200924122107490](https://gitee.com/ymyguang/picture/raw/master/img/image-20200924122107490.png)



### 配置项目

我们随便打开一个Java文件，选择编辑运行信息。

选择应用程序

![image-20200924143202649](https://gitee.com/ymyguang/picture/raw/master/img/image-20200924143238822.png)

### 完善配置信息

![image-20200924143238822](https://gitee.com/ymyguang/picture/raw/master/img/image-20200924143202649.png)

这里要选择的是，当前文件的类



注意这里是，要选择构建，要不然文件夹内的Java不能被转换成class文件；Javau运行的第二部都没有实现，Java肯定运行不起来；

![image-20200924143312319](https://gitee.com/ymyguang/picture/raw/master/img/image-20200924143312319.png)





配置好之后，小三角就可以点击了。![image-20200924143431252](https://gitee.com/ymyguang/picture/raw/master/img/image-20200924143431252.png)





我们点击三角号之后，会弹出报错

![image-20200924143520511](https://gitee.com/ymyguang/picture/raw/master/img/image-20200924143641465.png)

很明显，是因为idea所使用的jdk的版本太低，因此我们的任务就很明显，去修改jdk的版本。在设置中的找到如下选项：并将目标字节码版本改成8；	
![image-20200924143641465](https://gitee.com/ymyguang/picture/raw/master/img/image-20200924143520511.png)

再次点击运行后，程序就已经跑起来了；

![image-20200924143735483](https://gitee.com/ymyguang/picture/raw/master/img/image-20200924143735483.png)





此时我们的项目就已经可以运行了，值得注意的是，刚才我们选择了构建，所以该项目文件夹下的所有Java都被转换成class文件，因此单个项目文件中的Java运行的时候，就可以找到这个文件内的其他类。（Java在识别类的时候，只认识class，不认识Java）



#### 接下来，我们编写一个测试类

```java
package org.lionsoul.ip2region.test;

import java.io.File;
import java.lang.reflect.Method;
import java.util.Scanner;

import org.lionsoul.ip2region.DataBlock;
import org.lionsoul.ip2region.DbConfig;
import org.lionsoul.ip2region.DbSearcher;
import org.lionsoul.ip2region.Util;

public class IPUtil {
    public static String getCityInfo(String ip) {

        //db
        String dbPath = "C:\\Users\\admin.000\\Desktop\\java1\\src\\main\\java\\org\\lionsoul\\ip2region\\test\\ip2region.db";  //这里填写IP2region的绝对路径即可

        File file = new File(dbPath);
        if (file.exists() == false) {
            System.out.println("Error: Invalid ip2region.db file");
        }

        //查询算法
        int algorithm = DbSearcher.BTREE_ALGORITHM;  //B-tree
        //DbSearcher.BINARY_ALGORITHM //Binary
        //DbSearcher.MEMORY_ALGORITYM //Memory
        try {
            DbConfig config = new DbConfig();
            DbSearcher searcher = new DbSearcher(config, dbPath);

            //define the method
            Method method = null;
            switch (algorithm) {
                case DbSearcher.BTREE_ALGORITHM:
                    method = searcher.getClass().getMethod("btreeSearch", String.class);
                    break;
                case DbSearcher.BINARY_ALGORITHM:
                    method = searcher.getClass().getMethod("binarySearch", String.class);
                    break;
                case DbSearcher.MEMORY_ALGORITYM:
                    method = searcher.getClass().getMethod("memorySearch", String.class);
                    break;
            }

            DataBlock dataBlock = null;
            if (Util.isIpAddress(ip) == false) {
                System.out.println("Error: Invalid ip address");
            }

            dataBlock = (DataBlock) method.invoke(searcher, ip);

            return dataBlock.getRegion();

        } catch (Exception e) {
            e.printStackTrace();
        }

        return null;
    }


    public static void main(String[] args) throws Exception {
        String ip;
        Scanner in = new Scanner(System.in);
        ip = in.nextLine();
        System.err.println(getCityInfo(ip));
    }
}
```

继而去，重新构建一次，产生class文件，之后直接运行该文件中的main方法，即可

![image-20200924144312696](https://gitee.com/ymyguang/picture/raw/master/img/image-20200924144312696.png)

到这里整个项目就可以跑了；



### 后记

也许看着很简单，但是自己搞了很多天，不是因为出什么问题，而是不知道往哪里去想，这才是最耗时，最耗精力的东西。

其实更详细的东西肯定还有，起初我搜索了Junit，maven，搜索各种报错信息，更改JDK版本，等等等等，还好自己i坚持了下来，成功的那一刻，之前的苦逼碰壁完全值得了！

也许网上没有手把手的教程，可能是太简单了吧。嘤嘤嘤‘