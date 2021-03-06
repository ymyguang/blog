---
title: "C++中的数据转换"
date: 2020-03-04T15:03:16+08:00
draft: false
tags: ["computer", "C/C++","basic"]
categories: ["coding"]
---



### 数据转换

在做C语言题目的时候，有遇到了数据转换类型的题目。之前对这方面总是草草代过，今天终于给我了当头一棒。

现在对该`数据类型转换`总结整理一下

#### 自动数据转换

1. 将一种数据类型付给另一个数据类型的时候

   ```cpp
   float f = 100;
   ```

   这里f是float类型，而100确实int类型，此时编译器会自动把100转换为float类型。

   ```cpp
   int i = f;
   ```

   这时候，编译器就会把f转换成int类型，之后在进行赋值操作。



1. 不同类型数据运程中，规则如下

   ```
   编译器会自动把所占内存小的变量类型转换为所占内存多的数据类型，如图所示
   ```

   <img src="img/%E6%95%B0%E6%8D%AE%E8%BD%AC%E6%8D%A2/image-20200303151419119.png" alt="image-20200303151419119" style="zoom:50%;" />

   

   

#### 强制数据转换

非自动转化，而是按照自己的需求制定转换类型

```cpp
    规则：(type_name) expression  
    例如 k = (char)a；
实例：
#include <iostream>
using namespace std;
int main()
{
    int a = 10,b = 3;
    double c = (double)a/b //此时仅仅将a转换成double类型，就可以是编译器自动将结果转化成double类型变量
}
```

##### PS：转换数据类型都是临时，并不印象元数据的值！