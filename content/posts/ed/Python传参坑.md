---
title: "python参数传递"
draft: false
date: 2020-06-29T15:05:22+08:00
tags: ["coding","Python"]
categories: ["coding","Python"]
---



# Python传参坑

起初在学习Python中的yield中，以杨辉三角练手。

在网上找到了这样的一串代码

```python
L=[1]
yield L
while True:
	L=[([0]+L)[m]+(L+[0])[m] for m in range(len(L)+1)]
yield L
```

这段代码很奇妙，通过前后移位继而相加的方式，达到了杨辉三件的每一层。我想把他改写成for循环的方式，但是随着报错的不断深入，感觉对我而言并没有这么简单。

### 开始踩坑

这是最接近正确的代码

```python
def yh():
    L = [1]
    yield L
    while True:
        L1 = [0] + L
        L2 = L + [0]
        L.append(0)
        for m in range(len(L)):
            L[m] = (L1[m] + L2[m])
        yield L
results = []
n = 0
for t in yh():
    results.append(t)
    n = n + 1
    if n == 10:
        break

for t in results:
    print(t)
```

但是出现的问题是：存放结果的results中，一直是多个重复的list，经过调试发现，results中的值跟随着L的变化而变化。每当L被操作的时候，results中的结果也会发生变化。

为了解决这个问题，尝试了很多方法，开始时以为是yield的用法不理解，就一味去了解yield用法，但尽管知道了用法之后还是不能结果以上的问题。仔细想了一下，怀疑是results指向的对象和L所指向的对象是一个地址，所以导致了两者同时变化。事实证明猜想是正确的。

#### 迭代器–yield

为了解决列表占用大量内存空间的问题，迭代器就是一种时间换空间的解决方案，迭代器在执行过程中会对类似函数中的代码进行计算，如此一来就节省了空间；下面是yield的特点

- 特征一：含有return的功能，可以将值返回到调用位置
- 特征二：会记录“函数”上一次执行的位置，下次迭代时，会从上一个位置继续进行。因此迭代“函数的”内容是会被保存的，而且会记录上一次的执行位置。



于是我开始尝试使用return去解决问题，但是还是无济于事（当时还是没有意识到不是yield的问题）。

### 填坑

又经过一阵子搜索，发现Python中传参和C中的传参大为不同。在C中，默认的传参方式是传值调用。而在Python中，得益于一切皆为对象的原则，所以并没有默认的传参类型。

具体传参方式为：

- list等可变数据类型为所传参数时，是传地址。即C++中的引用。新变量名称仅仅指向赋值对象的地址（新变量**随**原参变化）
- tuple等不可变的数据类型为传值。即复该值后，开始重新赋值。（新变量**不随**原参变化）

如此一来，解决方法就很明显了，即使用传值。在Python传值需要copy模块。果不其然，使用传值方式后，问腿迎刃而解了。

附正确代码：

```python
    def yh():
        L = [1]
        yield L
        while True:
            L1 = [0] + L
            L2 = L + [0]
            L.append(0)
            for m in range(len(L)):
                L[m] = (L1[m] + L2[m])
            yield L
        
results = []
n = 0
for t in yh():
    results.append(t)
    n = n + 1
    if n == 10:
        break

for t in results:
    print(t)
```

### 战果

![image-20200708165430929](https://gitee.com/ymyguang/picture/raw/master/img/image-20200708165430929.png)