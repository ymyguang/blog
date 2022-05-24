# 跨域的一些知识


## 跨域的一些知识

#### 什么是跨域？

跨域是浏览器端为了防止恶意AJAX的请求，只允许同7的请求，禁止跨域请求，这里的同域要求是：同协议，同域名，同端口；只要有一项不一样，就k会被认为是跨域请求，浏览器就不会发起；实际上这种激进的方式，在现在的开发中跨域是一个很常见的请求，因此需要解决这种”拦截“；

在CORS问世之前，有各种曲线的方法来避免这种问题，比如说直接用同域发起请求，但是请求附加着实际的参数，后端拿到后，在向实际的域名发起请求，并将结果返回倒当前页面的Ajax，真正意义上来讲，这种方式并没有解决以上的问题，只是绕开了此项限制；但是CORS的出现便彻底解决了此问题，其方式就是在HTTP的请求头中加上一些字段，这些字段有的是写给浏览器的，有的是写给后端服务器的，不过实际上是取决于浏览器是否发送请求决定的；	

#### CORS(Cross-origin resource sharing)  跨域资源共享

CORE有两种方式，方式的选择取决于请求头的格式，请求头的格式分为：

- 简单请求
- 复杂请求

##### 简单请求

满足一下两项条件的都属于简单请求

1. 请求方法属于一下情况之一：
   - HEAD
   - GET
   - POST
2. HTTP请求头信息只包含一下字段：
   - Accept
   - Accept-Language
   - Content-Language
   - Last-Event-ID
   - Content-Type：只限于三个值`application/x-www-form-urlencoded`、`multipart/form-data`、`text/plain`



##### 复杂请求

显然，只要是不满足以上简单请求的，当人就是属于复杂请求





#### 对于请求的处理方式

浏览器对于不同的请求有着不通过的处理方式，想当然，对于简单的请求有着相对简单的处理方式

**直接在头部信息中添加字段：Origin**

> 如果`Origin`指定的源，不在许可范围内，服务器会返回一个正常的HTTP回应。浏览器发现，这个回应的头信息没有包含`Access-Control-Allow-Origin`字段（详见下文），就知道出错了，从而抛出一个错误，被`XMLHttpRequest`的`onerror`回调函数捕获。注意，这种错误无法通过状态码识别，因为HTTP回应的状态码有可能是200。

那什么情况下，才会被浏览器认为是合法的请求呢？（浏览器把这个请求发出去）

当浏览器自动发送请求后，收到的相应中包含以下字段时，本次请求才会生效

```http
Access-Control-Allow-Origin: http://api.bob.com
Access-Control-Allow-Credentials: true
Access-Control-Expose-Headers: FooBar
```

第一个字段代表的是服务器允许的域，可以是请求的域名，也可以是*，代表接受所有的跨异请求【必选】

第二个字段：代表是否发送Cookies【可选】

另一方面，开发者必须在AJAX请求中打开`withCredentials`属性。

> ```javascript
> var xhr = new XMLHttpRequest();
> xhr.withCredentials = true;
> ```

否则，即使服务器同意发送Cookie，浏览器也不会发送。或者，服务器要求设置Cookie，浏览器也不会处理。

但是，如果省略`withCredentials`设置，有的浏览器还是会一起发送Cookie。这时，可以显式关闭`withCredentials`。

> ```javascript
> var xhr = new XMLHttpRequest();
> xhr.withCredentials = false;
> ```

> 第三个字段可选。CORS请求时，`XMLHttpRequest`对象的`getResponseHeader()`方法只能拿到6个基本字段：`Cache-Control`、`Content-Language`、`Content-Type`、`Expires`、`Last-Modified`、`Pragma`。如果想拿到其他字段，就必须在`Access-Control-Expose-Headers`里面指定。上面的例子指定，`getResponseHeader('FooBar')`可以返回`FooBar`字段的值。



---

复杂请求的处理方式：

同简单请求方式不同，复杂的请求方式中，浏览器会先进行一场”预检“请求，只有当该请求满足条件时，才继续向 跨域站发起真正的请求；预检的请求头是：OPTIONS,下方图片就是预检的请求头；

![image-20210728235921577](https://gitee.com/ymyguang/picture/raw/master/img/image-20210728235921577.png)

只有当服务器所返回的消息头中包含字段`Access-Control-Allow-Origin`且字段的值是之前的origin域名或者是*（代表所有域名）时，浏览器才会真正的发出数据；除了以上的数据之外，还会存在以下两个表头：

```http
Access-Control-Allow-Methods: GET, POST, PUT
Access-Control-Allow-Headers: X-Custom-Header
```

​	第一个是返回所允许浏览器能够发送的请求方式

​	第二个是，返回服务器支持的所有信息字段

**以上，当第一次通讯成功后，接下来的浏览器请求就会向简单请求格式那样，直接在请求头中加入Origin的字段信息。同样，服务器返回的消息头中，也会包含Access-Control-Allow-Origin字段；**



参考地址：https://www.ruanyifeng.com/blog/2016/04/cors.html


