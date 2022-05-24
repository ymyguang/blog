# Spring笔记


## Spring 

#### 1.1、优点

1. ==**控制反转（incersion of control)  &&  依赖注入（dependency injection)**==

解释：依赖注入是控制反转的附属品。正是由于控制反转才能够产生依赖注入的机会；Ioc不是一个技术，而是一个编程的思想方法。主要就是将对象的控制权给控制器（controller）,这里所说的控制，也就是如此。

下面是反转，反转与之相对的正传，正转就是传统意义上的对象主动的创建资源，自给自足，需要什么就自己创建什么；反转指的是：这种需要什么就去创建什么的能力被剥夺，而是由控制器掌握；如此便达到了：有曾经的主动创建变成了当下的被动接受，上方所说的依赖注入就是对象被动的接受依赖形象的描述；

好处：进一步解构代码	关系。更方便代码复用和测试

IoC和DI的关系：

> IoC和DI由什么关系呢？其实它们是同一个概念的不同角度描述，由于控制反转概念比较含糊（可能只是理解为容器控制对象这一个层面，很难让人想到谁来维护对象关系），所以2004年大师级人物Martin Fowler又给出了一个新的名字：“依赖注入”，相对IoC 而言，**“****依赖注入”****明确描述了“被注入对象依赖IoC 容器配置依赖对象”。

Spring 核心的思想便是Ioc，在IoC模式下，控制权发生了反转，即从应用程序转移到了IoC容器，所有组件不再由应用程序自己创建和配置，而是由IoC容器负责，这样，应用程序只需要直接使用已经创建好并且配置好的组件。

2. **支持事务处理，框架整合支持**

3. **轻量级的，非侵入式的**

---

##### 控制反转样例

```java
import com.ymyguang.dao.UserDaoMysqlImpl;
import com.ymyguang.dao.UserDaoOracleImpl;
import com.ymyguang.service.UserService;
import com.ymyguang.service.UserServiceImpl;

/**
 * @another ymyguang
 * @date 7/22/2021 10:09 AM
 */
public class Mytest {
    public static void main(String[] args) {
        UserService userService = new UserServiceImpl();
        //控制权交给用户
        ((UserServiceImpl)userService).setUserDao(new UserDaoMysqlImpl());
        userService.getUser();
    }
}
```

#### 2、初探Spring

##### XML文件配置

Spring的关键在于配置文件，在常规的Java代码中，创建一个对象需要一下格式：
		类型名 变量名 = new 类型名，而在Spring当中，一切创建创建对象的操作，都可以由Bean来解决；同时还能向相关对象的属性，进行赋值；在Bean中，有以下格式：

```xml
<bean id = "Hello" class = "com.ymyguang.pojo"  >
	<property name = "str" value(ref) = "Hello"/>
</bean>
```

与正常创建对象格式相对应的是：

- id –> 变量名称    
- class –> 类型名
- name –> 字段名称
- value –> 基本数据类型   
- ref –> Spring容器已经引入的成员

##### Test文件测试

一个核心的关键：就是首先要获取容器的上下文，之后就可以再次获取由容器创建的对象（这里的对象是单例，只有一个，因此数据也是共享的）

```java
import com.ymyguang.pojo.Hello;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
 * @another ymyguang
 * @date 7/22/2021 11:44 AM
 */
public class Mytest {
    public static void main(String[] args) {
        //获取容器的上下文
        ApplicationContext context = new ClassPathXmlApplicationContext("Beans.xml");
        //获取所创建对象（容器创建），getBean中的字段就是Bean文件中的ID名称，实际上也就是创建的实例对象的变量名称
        Hello hello = (Hello) context.getBean("hello");
        System.out.println(hello.toString());
        System.out.println(hello.getHello());
```

**总结：到目前为止，Sping全局的接管了实例化对象的生命周期，其中包括创建，传递，销毁等等，在调整功能时，大大的简化了代码的修改，降低了各个对象之间的耦合性。==一个bean就是一个对象的实例化==**

#### 3、IOC创建对象的方式

一、无参数创建：实际上就是调用对应类的无参构造函数

二、有参数创建：同样是利用类的构造函数

1. 下标创建

   ```xml
   <bean id="user" class="com.ymyguang.pojo.User">
           <constructor-arg index="0" value="Liubiao"/>-->
           <constructor-arg index="1" value="2"/>-->
   </bean>
   ```

2. **属性名创建**

   ```xml
   <bean id="user" class="com.ymyguang.pojo.User">
       <constructor-arg name="age" value="12"/>
       <constructor-arg name="name" value="ymyguang"/>
   </bean>
   ```

3. 传入类型创建：字段：type ,有限制，不常用

**总结：Spring载入Bean时，所有的对象都已经被创建。且多次返回的是同一个对象**

#### 4、Spring文件配置

1. ##### import

   导入其他Bean文件，通常在团队开发当中使用；格式如下

   ```xml
   <import resource="Beans.xml"/>
   ```

2. ##### alias

   别名

   ```xml
   <alias name="user" alias="userT"/>
   ```

   

#### 5、依赖注入（Dependency Injection)

##### 5.1、基本类型注入 （set注入）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="address" class="com.ymyguang.pojo.Address">
        <property name="address" value="北京"/>
    </bean>
    <--!-->set注入</--!-->
    <bean id="student" class="com.ymyguang.pojo.Student">
        <property name="name" value="ymyguang"/>
        
        <property name="address" ref="address"/>
        
        <property name="books">
            <array>
                <value>西游记</value>
                <value>三国演义</value>
                <value>红楼梦</value>
            </array>
        </property>
        
        <property name="games">
            <list>
                <value>LOL</value>
                <value>CS</value>
            </list>
        </property>
        
        <property name="happy">
            <set>
                <value>足球</value>
                <value>篮球</value>
                <value>棒球</value>
            </set>
        </property>
        
        <property name="id">
            <map>
                <entry key="身份证" value="3131231313131"/>
                <entry key="IdCard" value="dedeeededede"/>
            </map>
        </property>
        
        <property name="male">
            <null/>
        </property>
    </bean>
</beans>
```

规律：基本上就是，对应的数据类型由对应的标签了，其内容在<value>当中；

##### 5.2、构造器注入

```xml
<bean id="user" class="com.ymyguang.pojo.User">
    <constructor-arg name="age" value="12"/>
    <constructor-arg name="name" value="ymyguang"/>
</bean>
```

##### 5.3、拓展注入

- p注入(property)

  - 类似于set注入

  - 使用之前，需要添加依赖

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:p="http://www.springframework.org/schema/p"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd">
        <bean id="hello" class="com.ymyguang.pojo.Hello" p:hello="你好，世界！" />
        <--这里的hello 就是类的属性 -->
    </beans>
    ```

    

- c注入(construct)

  - 相当于构造器注入

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:p="http://www.springframework.org/schema/p"
           xmlns:c="http://www.springframework.org/schema/c"
           xsi:schemaLocation="http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd">
        <bean id="hello" class="com.ymyguang.pojo.Hello" c:string="Hello World"/>
        <!--这里的c后面的值，就是构造器后面的参数名称-->
    </beans>
    ```

##### 5.4、bean的作用域(scope)

- singleton 单例模式

  ```xml
  <bean id="hello" class="com.ymyguang.pojo.Hello" scope="singleton"/>
  ```

- prototype 原型模式

  ```xml
  <bean id="hello" class="com.ymyguang.pojo.Hello" scope="prototype"/>
  ```

#### 6、自动装配

**自动装配：不需要再bean中的property中再次引入其beanid名称，可以按照指定的一定方式进行自动装配；**

关键词：**@autowire**

##### 6.1、Bean文件自动装配

类型：

- ByName：按照Bean的中set方法后面的名称查找与之对应的id的名称，自动装配；当Beans文件中，bean的ID不等于set后的名称，将会导致注入失败

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="dog" class="com.ymyguang.pojo.Dog"/>
    <bean id="cat" class="com.ymyguang.pojo.Cat"/>
    <bean id="people" class="com.ymyguang.pojo.People" p:name="ymyguang" autowire="byName"/>
</beans>
```

- ByType：按照Bean中类型,	在Bean整个文件中查找与之对应类型；需要注意的是：**当Beans文件中，有两个相同的类型时，就会造成异常**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="dog" class="com.ymyguang.pojo.Dog"/>
    <bean id="cat111" class="com.ymyguang.pojo.Cat"/>
    <bean id="people" class="com.ymyguang.pojo.People" p:name="yymyguang" autowire="byType"/>
</beans>
```

##### 6.2、注解@Autowired

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```

**注解自动装配有以下特征：**

1. **使用Autowired时，不需要编写set方法，即使编写也不会进调用**

2. @Autowired先按照byType进行匹配

3. @Resource 先按照byName进行匹配，再按照byType进行匹配；

4. 当同种类型的数量大于1时，@Autowirte的免配置装配将会失效，因为他不知道该注入同一个类型的哪一个bean文件；此时需要@Autowirte配套的注解：**@Qualifier**;使用格式如下：

   ​	@Autowired

   ​	@Qualifier(value=“xxx”)这里的xx就是指定bean组件的id；

   ```java
   package com.ymyguang.pojo;
   
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.beans.factory.annotation.Qualifier;
   
   /**
    * @another ymyguang
    * @date 7/22/2021 8:17 PM
    */
   public class People {
       private String name;
   
       @Autowired
       private Cat cat;
       @Autowired
       @Qualifier(value = "111")
       private Dog dog;
   
       public String getName() {
           return name;
       }
   
       public Cat getCat() {
           return cat;
       }
   
       public Dog getDog() {
           return dog;
       }
   
       @Override
       public String toString() {
           return "People{" +
                   "name='" + name + '\'' +
                   ", cat=" + cat +
                   ", dog=" + dog +
                   '}';
       }
   }
   ```

   **@Nullable,允许为null**

#### 7、使用注解开发

当jdk更新到1.5出现了注解，因此在Spring中，也可以使用注解进行开发，无论如何不通过，其最终方式都是类似Beans.xml的文件，供Spring容器进行托管；在Spring当中，使用注解开发必须引用aop包；在Beans.xml文件中需要引入一下代码，其功能是令 Spring扫描指定下的Bean组件；

```xml
<context:component-scan base-package="com.ymyguang.pojo"/>
```

前期准备：Beans.xml满足固定格式

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">
    
    <context:annotation-config/>
    <context:component-scan base-package="com.ymyguang.pojo"/>
				<!--bean组件允许不在此处配置，而是使用@Component注解-->
</beans>
```

1、注解**创建bean组件**

@Component注解，添加至类上方

```java
@Component // 声明。这是一个bean组件
public class Cat {
    private int ID;

    @Override
    public String toString() {
        return "Cat{" +
                "ID=" + ID +
                '}';
    }

    public int getID() {
        return ID;
    }

    public void setID(int ID) {
        this.ID = ID;
    }

    public void show() {
        System.out.println("miao~");
    }
}
```

2、注解**属性注入**

注解：@Value(“xxx”)

3、衍生注解

- @Repository
- @Service
- Controller

以上三个注解都等同于：@Component，只不过在MVC分层中，使用不同的注解来表示不同的含义

4、作用域

```
@Scope(“singleton”),			@Scope(“prototype”)
```



#### 8、Java配置文件

同城

##### 8.1、使用Java配置文件所需的准备

使用Java文件进行配置Spring文件，可以完全脱离Beans.xml文件；实际上只要在自己的类上添加注解：

**bean：**@Component就可以使Spring容器托管该类，就类似于在Beans.xml文件中，定义的bean组件一样，但是当使用第三方类的时候，无法在类的源码层面进行修改，因此会造成一定的不同；

**==配置文件==：**需要创建配置类，类的上部需要告诉Spring所在容器该类是一个配置类，因此需要加入注解：**@Configuration**

**扫描指定包下的bean组件**：**@ComponentScan(“包地址”)**

##### 8.2.1、本地类声明第三方组件

直接在类的上方添加注解：@Component，这里就类似xml文件当中的<bean id="" class="">

```java
package com.ymyguang.pojo;

import org.springframework.stereotype.Component;

/**
 * @another ymyguang
 * @date 7/22/2021 8:18 PM
 */
@Component
public class Cat {
    private int ID;

    @Override
    public String toString() {
        return "Cat{" +
                "ID=" + ID +
                '}';
    }

    public int getID() {
        return ID;
    }

    public void setID(int ID) {
        this.ID = ID;
    }

    public void show() {
        System.out.println("miao~");
    }
}
```

##### 8.2.1、第三方类声明bean组件

**==@Bean，在配置类内，使用注解，向容器声明bean文件；==**

配置文件

```java
@Configuration  //声明该类是配置类
@ComponentScan("com.ymyguang.pojo") // 扫描指定下的bean组件
public class myConfig {
    // 创建一个Bean:
    @Bean
    ZoneId createZoneId() {
        return ZoneId.of("Z");
    }
}
```

测试文件

```java
import com.ymyguang.config.myConfig;
import com.ymyguang.pojo.Cat;
import com.ymyguang.pojo.People;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

/**
 * @another ymyguang
 * @date 7/23/2021 4:09 PM
 */
public class myTest {
    public static void main(String[] args) {
        ApplicationContext context = new AnnotationConfigApplicationContext(myConfig.class);
        People people = context.getBean("people", People.class);
        Cat cat = context.getBean(Cat.class);
        System.out.println(cat.toString());
        System.out.println(people.toString());
    }
}
```

#### 9、AOP

AOP(Aspect Oriented Programming)面向切面编程；在不改变原有的源代码上，实现功能的完善，也就是所说的在 功能的切面上织入一个功能；通常使用代理模式用于实现；

例如，一个图书管理系统，最基本的功能就是满足增删改查操作唉，但是需要增加功能时，按照一般思路会直接从本身的源代码进行修改，不过使用AOP的思路，可以重新重新创建。直接添加代理模式，在原有的类上，重新延申出来一个代理类，在代理类中，添加修改额外的功能；

##### 9.1、代理模式

**成员**：

- 真实对象：需要被代理的成员【提供服务】
- 代理对象：代理别人的成员【托管服务】
- 客户端：使用服务的成员【使用服务】

**好处：**

1. 更容易维护原子类
2. 使原子类更加的纯净，不需要关注其他的额外服务；
3. 大大的解耦，使代码的复用程度增加；

**静态代理**：在为编译之前就写好了代理类，之所以称之为静态，是因为在Java加载过程中，没有代理对象的生成。正因为在编译之前需要提前写好代理类，因此也导致了静态代理的有一定的不足：需要编写重复的代码，降低了开发效率；不过，相对于缺点而言，具有以上所属的优点；

**动态代理**

- 基于接口-java动态代理
- 基于类:cglib
- 基于字节码：javassist

所谓动态，就是与静态相对应的，那就是不需要提前生成所需要的代码，完全通过运行时生成所需代理的代理类；因此在这里离不开发反射技术，毋庸置疑，动态代理不仅由代理模式的优点，而且还能通用的使用方法，完全避免了静态代理的缺点；

以下是Java中，使用内置的功能实现的**动态代理类**；

```java
package com.ymyguang.demo2;

import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

/**
 * @another ymyguang
 * @date 7/26/2021 9:10 AM
 */
public class InvocationHandler implements java.lang.reflect.InvocationHandler {
    // 获取需要代理的对象
    private Object object;

    public void setObject(Object object) {
        this.object = object;
    }
   -----------------------------------------------------------------------
    //  创建并返回传入类的代理类 
    public Object getProxy() {
        return Proxy.newProxyInstance(this.getClass().getClassLoader(), this.object.getClass().getInterfaces(), this);
    }
  -------------------------------------------------------------------------
    //  使用被代理类类的指定函数
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        log(method.getName());// DIY功能
        return method.invoke(object, args);
    }

    private void log(String msg) {
        System.out.println("使用了" + msg + "方法");
    }
}
```

```Java
package com.ymyguang.demo2;

/**
 * @another ymyguang
 * @date 7/24/2021 9:36 PM
 */
public class UserServiceImpl implements UserService{
    @Override
    public void creat() {
        System.out.println("创建一个表");
    }

    @Override
    public void add() {
        System.out.println("添加");
    }

    @Override
    public void delete() {
        System.out.println("删除");
    }

    @Override
    public void query() {
        System.out.println("查询");
    }
}
```

**使用动态代理类**

```java
package com.ymyguang.demo2;

/**
 * @another ymyguang
 * @date 7/24/2021 9:45 PM
 */
public class Client {
    public static void main(String[] args) {
        // 创建真实对象
        UserServiceImpl userService = new UserServiceImpl();
        -----------------------------------------------------------
		//          	    生成代理类的准备代码
        // 创建生成动态代理对象，仍然是工具对象；
        InvocationHandler handler = new InvocationHandler();
        // 设置代理目标
        handler.setObject(userService);
        ------------------------------------------------------------

        // 得到生成代理类
        UserService proxy = (UserService)handler.getProxy();

        proxy.query();
    }
}
```

##### 9.2、AOP的实现方式

关于AspectJ的一些背景知识

> AOP is a programming paradigm that aims to increase modularity by allowing the separation of cross-cutting concerns. It does so by adding additional behavior to existing code without modification of the code itself. Instead, we declare separately which code is to modify.

这一节实现的比较困难，主要原因是第一次接触这些东西；其中各种专有名词充当了很大一部分的拦路虎；但是其实也只是动态代理的那些东西；

- 横向关注点：就是所需要引入的服务
- 切面：实际上就是以上所引入服务的类
- 通知：就是切面的方法
  - 前置通知
  - 后置通知
  - 环绕通知
  - 异常抛出通知
  - 引介通知 
- 目标：被通知的对象
- 代理：代理目标生成的类
- 切入点：织入类中的位置

###### 使用Spring API方式实现

使用准备

1. 导入包

   ```xml
           <dependency>
               <groupId>org.aspectj</groupId>
               <artifactId>aspectjweaver</artifactId>
               <version>1.9.7</version>
               <scope>runtime</scope>
           </dependency>
   ```

   

2. 创建xml文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:aop="http://www.springframework.org/schema/aop"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/aop
           https://www.springframework.org/schema/aop/spring-aop.xsd">
   </beans>
   ```

   

3. 编写xml文件内容

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">

    <bean id="log" class="com.ymyguang.log.Log"/>
    <bean id="afterLog" class="com.ymyguang.log.AfterLog"/>
    <bean id="userService" class="com.ymyguang.demo2.UserServiceImpl"/>

    <aop:config>
        <aop:pointcut id="pointcut" expression="execution(* com.ymyguang.demo2.UserServiceImpl.*(..))"/>
        <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>
    </aop:config>

</beans>
```

**切面样例代码**

```java
package com.ymyguang.log;

import org.springframework.aop.AfterReturningAdvice;

import java.lang.reflect.Method;

/**
 * @another ymyguang
 * @date 7/26/2021 11:40 AM
 */
public class AfterLog implements AfterReturningAdvice {
    @Override
    public void afterReturning(Object o, Method method, Object[] objects, Object o1) throws Throwable {
        System.out.println("执行了"+method.getName()+ "以下是返回方法" + o);
    }
}
```

###### 使用第三方切面

xml代码：

```xml
<aop:config>
    <!--切面,指定引入的Bean-->
    <aop:aspect ref="diy">
        <!-- 切入点，指定拦截对象-->
        <aop:pointcut id="point" expression="execution(* com.ymyguang.demo2.UserServiceImpl.*(..))"/>
        <!-- 通知，执行对应方法-->
        <aop:before method="before" pointcut-ref="point"/>
        <aop:after method="after" pointcut-ref="point"/>

    </aop:aspect>
</aop:config>
```

###### 使用注解实现AOP

**切面：@Aspect**

**执行前切入：@Before(execution())**

**执行后切入：@Afer(execution())**



