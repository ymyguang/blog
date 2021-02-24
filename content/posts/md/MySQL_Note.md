---
title: "Mysql 增删查改"
draft: false
date: 2020-06-29T15:05:22+08:00
tags: ["coding", "basic","sql"]
categories: ["coding","basic","sql"]
---



# MySQL Note

## 概念



### 用法

**SELECT 选择列（column) 即，输出列；**

- 用法  SELETE 列名 FROM  表单；

  - 执行顺序 FROM -> SELECT

- 例子：

  ```sql
  SELECT 
  	customerNumber
  FROM 
  	customers;
  ```

  结果：

  ![image-20201029110850231](https://gitee.com/ymyguang/picture/raw/master/img/image-20201029110850231.png)

**ORDER排序语句【ORDEN BY】**

- 用法：伴随着SELECT使用，对筛选出来的结果进行排序

  - 升序：ASC  —asc**ending**
  - 降序：DESC  —desc**ending**

  例子：

  ```sql
  SELECT 
  	contactLastname,
  	contactFirstname
  FROM
  	customers
  ORDER BY
  	contactLastname DESC,
  	contactFirstName ASC;
  ```

  结果：

  ![image-20201029112432550](https://gitee.com/ymyguang/picture/raw/master/img/image-20201029204156561.png)

  需要注意的是，多个排序的时候，是对前面已经排序之后的结果进行下一个列中的排序，即在第一个排序的基础上，进行排序（第一个不会再发生变化）

  

  **WHER筛选语句**

  - 基本用法：搭配SELECT…..FROM…..使用

    - SELECT ….. FROM…… WHERE….

  - 例子：
  
    ```sql
  SELECT 
    	officeCode
FROM 
    	employees
    WHERE 
    	officeCode > 1 
    ```
    
    结果：
    
    ![image-20201029204156561](https://gitee.com/ymyguang/picture/raw/master/img/image-20201029112432550.png)
    
    - 可配参数：
    
    - ```sql
      [AND] [OR] [IN] [LIKE] [BETWEEN * AND *] [IS NULL]
      ```
    
	 - 操作符号：
    
      ![image-20201029204627660](https://gitee.com/ymyguang/picture/raw/master/img/image-20201029204627660.png)
  

