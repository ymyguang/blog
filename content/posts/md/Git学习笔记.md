---
title: "Git 笔记"
date: 2021-02-24T15:03:16+08:00
draft: false
tags: ["git","basic"]
categories: ["basic"]
---













### 2021年6月7日更新

**关于commit切换的一些记录**

分支、commit切换：git checkout 版本/commit标志MD5.

1. 切换的时候，不会对本身内容进行任何的修改，同时也不会丢失任何内容！但是工作区的内容是会变的（变成了指定的checkout，但是实际上没有任何变化）
2. 不管有多少分支，实际上都是跟着commit的提交而改变的，只不过是commit的提交在当前分支下有效而已。即内容版本是已经隐式的先前更新了；
3. git merge的合并若产生冲突>>>>>>到=========之间是第一个，从========到<<<<<<<<<<<<<<<<<<<<是第二个，也就是说=========是冲突的分界线
   - 手动修复好冲突之前，终端保持在一个挂起的状态
   - 修改之后，还需要重新提交代码（因为合并类似一个修改代码的操作）
4. git 用好了真的事半功倍！







### 2021年2月24日更新

#### 撤销操作

git checkout.#丢弃所有修改或删除，不影响新增或未跟踪的文件。会递归下级目录

git clean-nxdf#dry-run，先看下即将删除那些文件，再执行下面的危险命令

git clean-df#删除所有未跟踪的文件和目录(不包含.gitignore的文件），危险命令

git clean-xdf#删除所有未跟踪的文件和目录（同时包含.gitignore的文件），危险命令



代理操作：

```
git config --global proxy.http="http://127.0.0.1:1080"
git config --global proxy.https="socks://127.0.0.1:1080"

撤销：
git config --global --unset http.proxy
git config --global --unset https.proxy
```



## **Git笔记**

####  **1. Git是什么**

**Git是世界上最先进的分布式版本控制系统**

**<img src="https://mmbiz.qpic.cn/mmbiz_png/e1jmIzRpwWiaEynpFwWSmr59icj386rKKxiaCC3m4XHaxHaaqLkYlukTUALnHN74icx3VZyIM3uEXz7JA9ldicwe8BQ/640?tp=webp&amp;wxfrom=5&amp;wx_lazy=1&amp;wx_co=1" alt="img" style="zoom: 50%;" />**

- **Workspace：工作区**
- **Index/Stage：暂存区**
- **Repository：仓库区（本地仓库）**
- **Remote：远程仓库**

#### **2. 基本操作**


**文件操作：**

```

创建文件：touch 文件名.*
删除文件：rm 文件名
创建文件夹：mkdir 文件夹名
删除文件夹：rm -r 文件夹名
查看文件：cat 文件名
```

**恢复历史版本**

```
git reset --hard HEAD^  退回上一个版本
git reset --hard HEAD^^ 退回上上一个版本
git reset --hard HEAD^^^ 退回上上上一个版本
git reset --hard HEAD~100 退回第100个版本
git reset --hard 版本号 退回版本号版本 (查看版本号:git reflog)
```

**修改相关：**

```
放弃历史暂存区文件修改：git restor
git chackout -- 文件名
```

 ```git
初始化仓库：git init
 ```

 ```
添加文件到暂存区 ：git add filename
 ```

 ```
提交文件到仓库：git commit filename
 ```

 ```
查看暂存区当前状态：git status
 ```

  ```
查看暂存区文件修改情况：git diff
  ```

```
查看最新文件修改记录：git log   简洁呈现：git log -pretty=online
查看所有文件更改记录：git reflog
```

```
清空暂存区：git rm --cached filename;
		re ./git/index  暂存区文件
```

**分支：**

```
创建分支：git branch [分支名称]
查看分支：git branch -v
切换分支：git checkout [分支名称]
合并分支：git merge [要求被合并的分支名称]
```
**远程：**

```
推送：git push origin master
拉取：git pull origin master
pull和clone的区别，pull是拉去远程的某个分支，clone是直接把远程的所有信息都下载下来，比如日志信息，其他分支等
```





#### **时光穿梭机（版本恢复）**

**![image-20200304155405798](https://gitee.com/ymyguang/picture/raw/master/img/image-20200304211112056.png)**

##### **命令说明：**

- **git rm 删除工作区文件，并且把删除记录提交到暂存区（git rm[name]相当于：rm + git add[name] ）**
- **rm 删除工作区的文件。**

##### **恢复的方法：**

- **git checkout  filename**      
  - **对于checkout的说明：**
    - **情况一：当暂存区存在该文件时，将暂存区文件恢复到工作区**
    - **情况二：当暂存区未存在该文件时，将版本库中的文件恢复到工作区**
- **git reset HEAD^ filename** 
  - **作用：把上一个版本filename拉取到暂存区，此时工作区并不存在该文件，但是当我们执行git checkout  filename      文件在工作区就会被找到！**

##### **尝试情况：**

- **情况一：提交到暂存区，不提交文件库**
  - **rm删除，工作区文件不存在，暂存区没有同步最新情况**
    - **恢复**
      - **git checkout filename  工作区恢复成功，来源暂存区，可以看出add是把文件放到暂存区中**
      - **git reset HEAD filename 恢复失败（考虑原因是因为没有可恢复的仓库）**
  - **git rm删除，工作区文件不存在，暂存区更新删除记录，即暂存区该文件执行删除操作**
    -  	**git checkout filename  恢复失败（原因：因为给git rm=rm + add,此时暂存区该文件的记录，因此checkout会尝试在暂存区中恢复文件到工作区，但是暂存区中只有该文件的删除行为，而文件却不存在，因此恢复失败）**
    - **git reset HEAD^ -- filename恢复成功**



- **情况二：提交到暂存区，提交到文件库**
  - **rm删除，工作区文件不存在，暂存区存在**
    - **git checkout filename  工作区恢复成功**
    - **git reset HEAD^ -- filename恢复成功**
  - **git rm删除**
    - **git checkout — filename  恢复失败**
    - **git reset HEAD^ filename 此时操作区并未恢复文件 当在执行：git checkout filename  此时，工作区恢复了该文件**



#### **3. 错误异常**

**1.**

```
执行：git push origin master
报错：error: src refspec master does not match any，远程仓库与本地仓库的之前版本不匹配，无法进行不丢失的推送（合并）
解决方法：git pull origin master     git add   git commoit -m "x"
将远端的仓库拉下来，并于本地合并，之后再进行网络推送就可以了

```

解决链接：[https://blog.csdn.net/dietime1943/article/details/85682688?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task](https://blog.csdn.net/dietime1943/article/details/85682688?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task)

**2.**

```
执行：git push origin master
报错： Updating an unborn branch with changes added to the index.（使用添加到索引中的更改更新未生成的分支。） 其实也就是本地的仓库没有提交，无法推送远程仓库
解决方法：git commit -m "m"//作用：把暂存区的操作同步到本地的仓库中
```



#### 情况一：

未commit（有add/无add，均无效）

error: failed to push some refs to 'https://github.com/ymyguang/text.git'

![image-20200304210635259](https://gitee.com/ymyguang/picture/raw/master/img/image-20200304155405798.png)

提交后，commit，推送完成

#### 情况二：

![image-20200304211112056](https://gitee.com/ymyguang/picture/raw/master/img/image-20200304210635259.png)

本地编辑文件后，GitHub同时编辑文件后，推送本地仓库，失败，此时情况已提交

操作：pull拉取后，要求输入拉取说明。拉取成功。本地文件填充，（非同行）

操作后，再次推送，成功。（怀疑是检测版本号）

尝试使用--rebase ，拉回2.txt，再次提交后，正常，推送正常 

尝试本地删除，2.txt,不新建，直接推送，成功

，新建其他文件，推送，成功！



尝试修改不同文件：在两端同时修改，此次报错，由此可见：只要远端的版本不是与本地的版本书不匹配，将无法push

分析：如图

![image-20200304233241691](https://gitee.com/ymyguang/picture/raw/master/img/image-20200304233241691.png)

### Git文件恢复问题



设想一下，你有一个文件夹，打算自己的去DIY，但是由于自己的失误，把整个文件夹都搞得乱哄哄的这时候就需要Git来解决。



在Git中分为三个区域，分别是工作区，暂存区，仓库区

##### 作用

当我们修改文件，就在工作区

修改文件后，把需要提交的文件，放到暂存区

确定需要的文件都在暂存区之后，将暂存区的文件传到仓库区

##### 撤回文件：

- 丢弃工作区的修改

- - 文件的**修改**：checkout 撤销对**原存在文件**的修改，注意，是撤销对**原文件（最近一次add或者是commit**）的修改，对于新的文件的增加，和新的文件的删除，将无法恢复。

    （丢弃工作区对先前存在文件的修改，使之恢复到最近的一次add或者commit。）

  - 文件夹的**创建**：git clean .使整个文件区的任何操作进行撤销。



- 丢弃（退回）暂存区修改：git reset HEAD  <file>,此时暂存区域的文件就被退回到工作区，如果我们需要继续清除工作区 ，直接重复第一步的操作就可以了

