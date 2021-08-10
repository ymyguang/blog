---
title: ”Java导出Excel复盘"
date: 2021-08-10T10:27:56+08:00
draft: false
tags: ["jAVA","problem","reproduce"]
categories: ["problem","java","reproduce"]
---

## Java导出Excel复盘

在一个项目中，需要实现从数据库拿到数据，最后生成Excel文件返回到前台；

在此期间遇到的问题有：

- 返回前台，文件名称指定无效
- 如何 将数据转换到Excel格式
- Excel表格结构问题
- map放到List中，map.cleaer后，list数据消失问题  –> 克隆Map的方式
- 如何输出当前时间的方式

整体而言，其中最大的问题就是：**如何指定Excel的格式问题**，不过记录形式，还是按照逻辑顺序写；

### 数据转换到Excel形式

使用EasyPoi包，这个包是在POI的基础上二次封装的，简化了操作的方式；使用的方式有两种

- 简单形式的Excle使用注解方式
- 使用模板，自定义Excel样式；

两种的方法不同，但是都是通过List和Map来实现数据和Excel之间的转换的。主要数据内容实体是放在List中。最后使用提供的类方法直接转换到Excel文件；

起初使用的是Excel,但是只能使用包内内置的集中格式，但是满足不了需求；使用内置的样式主要时使用注解；

| 属性           | 类型     | 默认值           | 功能                                                         |
| :------------- | :------- | :--------------- | :----------------------------------------------------------- |
| name           | String   | null             | 列名,支持name_id                                             |
| needMerge      | boolean  | fasle            | 是否需要纵向合并单元格(用于含有list中,单个的单元格,合并list创建的多个row) |
| orderNum       | String   | “0”              | 列的排序,支持name_id                                         |
| replace        | String[] | {}               | 值得替换 导出是{a_id,b_id} 导入反过来                        |
| savePath       | String   | “upload”         | 导入文件保存路径,如果是图片可以填写,默认是upload/className/ IconEntity这个类对应的就是upload/Icon/ |
| type           | int      | 1                | 导出类型 1 是文本 2 是图片,3 是函数,10 是数字 默认是文本     |
| width          | double   | 10               | 列宽                                                         |
| height         | double   | 10               | **列高,后期打算统一使用[@ExcelTarget](https://github.com/ExcelTarget)的height,这个会被废弃,注意** |
| isStatistics   | boolean  | fasle            | 自动统计数据,在追加一行统计,把所有数据都和输出[这个处理会吞没异常,请注意这一点] |
| isHyperlink    | boolean  | false            | 超链接,如果是需要实现接口返回对象                            |
| isImportField  | boolean  | true             | 校验字段,看看这个字段是不是导入的Excel中有,如果没有说明是错误的Excel,读取失败,支持name_id |
| exportFormat   | String   | “”               | 导出的时间格式,以这个是否为空来判断是否需要格式化日期        |
| importFormat   | String   | “”               | 导入的时间格式,以这个是否为空来判断是否需要格式化日期        |
| format         | String   | “”               | 时间格式,相当于同时设置了exportFormat 和 importFormat        |
| databaseFormat | String   | “yyyyMMddHHmmss” | 导出时间设置,如果字段是Date类型则不需要设置 数据库如果是string 类型,这个需要设置这个数据库格式,用以转换时间格式输出 |
| numFormat      | String   | “”               | 数字格式化,参数是Pattern,使用的对象是DecimalFormat           |
| imageType      | int      | 1                | 导出类型 1 从file读取 2 是从数据库中读取 默认是文件 同样导入也是一样的 |
| suffix         | String   | “”               | 文字后缀,如% 90 变成90%                                      |
| isWrap         | boolean  | true             | 是否换行 即支持\n                                            |
| mergeRely      | int[]    | {}               | 合并单元格依赖关系,比如第二列合并是基于第一列 则{0}就可以了  |
| mergeVertical  | boolean  | fasle            | 纵向合并内容相同的单元格                                     |
| fixedIndex     | int      | -1               | 对应excel的列,忽略名字                                       |
| isColumnHidden | boolean  | false            | 导出隐藏列                                                   |

以上注解放在实体类上，位置是在字段的上方。

#### 注解

##### 	实体类：

```java
package com.exampl.news;


import cn.afterturn.easypoi.excel.annotation.Excel;

/**
 * Created by JueYue on 2017/8/25.
 */
public class OneTest {
        @Excel(name = "姓名")
        String name;
        @Excel(name = "年龄")
        int age;
        @Excel(name = "学号")
        String id;

    public OneTest(String name, int age, String id) {
        this.name = name;
        this.age = age;
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }
}
```

##### 使用

直接将实体的实例化对象放到List列表中，最后使用包内提供的导出类，直接导出为Workbook形式的对象；

输出到文件

```java
package com.exampl.news;

import cn.afterturn.easypoi.excel.ExcelExportUtil;
import cn.afterturn.easypoi.excel.entity.ExportParams;
import cn.afterturn.easypoi.excel.entity.params.ExcelExportEntity;
import com.exampl.news.entity.excel.AttributesTotalEntity;
import org.apache.poi.ss.usermodel.Workbook;

import java.io.*;
import java.util.ArrayList;
import java.util.List;

/**
 * @author ymyguang
 * @date 8/10/2021 4:34 PM
 */
public class TestMain {
    public static void main(String[] args) throws IOException {
        List<OneTest> list = new ArrayList<OneTest>();

        for (int i = 0; i < 3; i++) {
            list.add(new OneTest("学生" + i,i + 12,"BD" + i));
        }

        Workbook workbook = ExcelExportUtil.exportExcel(new ExportParams("TitleName","sheet1"), OneTest.class, list);
        FileOutputStream out = new FileOutputStream("C:\\Users\\admin.000\\Desktop\\web\\demo.xls");
        workbook.write(out);
        out.close();
    }
}
```

![](https://gitee.com/ymyguang/picture/raw/master/img/image-20210810165254138.png)

#### 模板方式（表结构问题）

模板，前提是配置好一个xls文件，其中表内字段有指定的代表方式。这里有一个简单的规则：

{{$fe: maplist t t.id }}    解释说明一下

- {{}}代表标签边界
- fe 代表是，使用的fe的写法
- maplist Java代码中，存放整个表格内容map变量名称
- t 单个数据元素
- t.id  一行数据中，是由map构成，id 就是Map的key,要求填入的就是对应的Value；

几点说明：

- 整个Excel的内容是存放到hashMap当中的。
- 主要数据内容，存放到List列表当中，最后存放map对象中，这里的键需要跟标签中的:后面的名称相同。这里是maplist，也就是Java中的map必须包含名为,maplist的键；list集合就是他的值
- list集合中的元素是map，map的键需要和标签中的t.xx的xx同名；

##### 演示

###### 模板文件配置:

![模板s](https://gitee.com/ymyguang/picture/raw/master/img/26145656_oqzr.png)

代码配置

```java
    public void fe_map() throws Exception {
        TemplateExportParams params = new TemplateExportParams(
                "WEB-INF/doc/专项支出用款申请书_map.xls");
        Map<String, Object> map = new HashMap<String, Object>();
        map.put("date", "2014-12-25");
        map.put("money", 2000000.00);
        map.put("upperMoney", "贰佰万");
        map.put("company", "执笔潜行科技有限公司");
        map.put("bureau", "财政局");
        map.put("person", "JueYue");
        map.put("phone", "1879740****");
        List<Map<String, String>> listMap = new ArrayList<Map<String, String>>();
        for (int i = 0; i < 4; i++) {
            Map<String, String> lm = new HashMap<String, String>();
            lm.put("id", i + 1 + "");
            lm.put("zijin", i * 10000 + "");
            lm.put("bianma", "A001");
            lm.put("mingcheng", "设计");
            lm.put("xiangmumingcheng", "EasyPoi " + i + "期");
            lm.put("quancheng", "开源项目");
            lm.put("sqje", i * 10000 + "");
            lm.put("hdje", i * 10000 + "");

            listMap.add(lm);
        }
        map.put("maplist", listMap);

        Workbook workbook = ExcelExportUtil.exportExcel(params, map);
        File savefile = new File("D:/excel/");
        if (!savefile.exists()) {
            savefile.mkdirs();
        }
        FileOutputStream fos = new FileOutputStream("D:/excel/专项支出用款申请书_map.xls");
        workbook.write(fos);
        fos.close();
    }
```

效果预览：

![模板导出效果](https://gitee.com/ymyguang/picture/raw/master/img/26150826_DKZQ.png)



### 返回前端文件名有误问题

前端下载文件，文件名取决于后端中对头部字段的设置；

设置的内容有：

- 字符产编码格式
- 内容格式
- 内容描述（文件名称就在这里）

在Content-Disposition,指定文件名称，但是此前是无效的，通过查找得到以下解决方式

对需要设置的文件名，进行URL编码，将编码之后的内容返回到前台，即可解决；

**URLEncoder.encode(fileName,"UTF-8")**

```java
response.setCharacterEncoding("UTF-8");
response.setHeader("content-Type", "application/vnd.ms-excel");
response.setHeader("Content-Disposition", "attachment;filename=" + URLEncoder.encode(fileName,"UTF-8"));
```



### map放到List中，map.cleaer后，list数据消失问题

此问题导致的原因是，map被add到List中，仅存放的是地址，因此当map.clear()执行后，地址位置没有对应的内容，因此为空；解决方式也很容易想到：clone当前map,因此下面实际中clone map的几种方式,，具体方式详见代码；以下方式均是浅拷贝

```java
package com.exampl.news;

import org.junit.jupiter.api.Test;
import java.util.HashMap;
import java.util.Map;
public class OneTest {
    @Test
    public void test() {
        Map<String, String> map = new HashMap<>();
        Map<String, String> map2 = new HashMap<>();
        map.put("id", "1");
        System.out.println("原始Map:" + map.get("id"));
//  浅拷贝：
//        clone拷贝
//        Map map2 = (Map) ((HashMap<?, ?>) map).clone();

//        初始化拷贝
//        Map map2 = new HashMap(map);

//        赋值拷贝
//        Map map2 = new HashMap();
//        map2.putAll(map);

//        Set实体传入
//        Map<String, String> map2 = new HashMap();
//        Set<Map.Entry<String, String>> set = map.entrySet();
//        for (Map.Entry<String, String> mapEntry : set) {
//            map2.put(mapEntry.getKey(), mapEntry.getValue());
//        }

        map.put("id", "22");
        System.out.println("当前Map:" + map.get("id"));
        System.out.println("克隆Map:" + map2.get("id"));
    }
}
```

### 获取当前时间

```java
Calendar c = new GregorianCalendar();
c.setTime(new Date());
SimpleDateFormat formatter = new SimpleDateFormat("dd-MM-yyyy HH:mm:ss");  
formatter.format(c,getTime());
```

