---
title: ""
date: 2021-08-04T18:08:47+08:00
draft: false
tags: ["","",""]
categories: ["","",""]
---

## JAVA I/O

#### 元类

**InputStream：Java中最基本的输入流，输入形式是字节**

**OutStream：Java中最基本的输出流，输出形式是字节**

**Reader：输入形式是字符。字符可以指定**

**Writer：输出形式字符，字符格式可以指定**



#### 常用类

BufferedInputStream

BufferedOuputSteam

BufferedInputStreamReader：提供字节转字符的转接桥

BufferedOutputStreamReader：输出字节转输出字符的转接桥

Buffered是为了提高效率，在内存中开辟一个缓存空间，读满当前缓冲的大小或者是读完当前文件后才将数据发送给Java文件处理程序；

以下是两者效率粗略的对比代码：

```java
import java.io.BufferedInputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.util.Calendar;

/**
 * @another ymyguang
 * @date 8/4/2021 9:59 PM
 */
public class BufferedTest {
    public static void main(String[] args) throws IOException {
        long start,end;
        File filepath = new File("C:\\Users\\admin.000\\Desktop\\22.txt");



        FileInputStream fileInputStream11 = new FileInputStream(filepath);
//        start = Calendar.getInstance().getTimeInMillis();
//        while(fileInputStream11.available() > 0)
//            fileInputStream11.read();
//        end = Calendar.getInstance().getTimeInMillis();
////        System.out.println("非Buffered时间差为：" + (end - start) + "\n ==========================================");

        start = Calendar.getInstance().getTimeInMillis();
        FileInputStream fileInputStream = new FileInputStream(filepath);
        while(fileInputStream.available() > 0)
            fileInputStream.read();
        end = Calendar.getInstance().getTimeInMillis();
        System.out.println("非Buffered时间差为：" + (end - start) + "\n ==========================================");

        start = Calendar.getInstance().getTimeInMillis();
        BufferedInputStream br = new BufferedInputStream(new FileInputStream(filepath));
        while(br.available() > 0) {
            br.read();
        }
        end = Calendar.getInstance().getTimeInMillis();
        System.out.println("Buffered时间差为：" + (end - start) + "\n ==========================================");
        br.close();
    }
}
```

![image-20210804230450105](https://gitee.com/ymyguang/picture/raw/master/img/image-20210804230450105.png)









![image-20210804215610513](https://gitee.com/ymyguang/picture/raw/master/img/image-20210804215612474.png)

![Javadoc For Dummies_ java.io Class Hierarchy Diagram](https://gitee.com/ymyguang/picture/raw/master/img/Javadoc For Dummies_ java.io Class Hierarchy Diagram.gif)



![readerHierarchy](https://gitee.com/ymyguang/picture/raw/master/img/readerHierarchy.gif)



![](https://gitee.com/ymyguang/picture/raw/master/img/writerHierarchy.gif)



![image-20210805093939350](https://gitee.com/ymyguang/picture/raw/master/img/image-20210805093939350.png)

![outputStreams](https://gitee.com/ymyguang/picture/raw/master/img/outputStreams.gif)















![java_io (1)](https://gitee.com/ymyguang/picture/raw/master/img/java_io (1).png)