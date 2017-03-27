---
title: Cookie的增删改
date: 2016-08-31 17:07:30
categories: Web
tags:
 - jquery
 - web前端
---
**cookie**
> Cookei存储在浏览器中的变量一小段的文本信息，在客户端中记录用户信息。区别于Cookei的Session是通过服务端记录用户信息确定用户身份。

* * * * *
通过[jquery.cookie(）](http://pan.baidu.com/s/1kVQ3W2N)的方法对Cookie进行简单的增删改查操作，jquery.cookie()是一个cookie插件，使用前要先依赖jquery库
<!--more-->

``` stylus
<script type="text/javascript" src="js/jquery-1.6.2.min.js"></script> 

<script type="text/javascript" src="js/jquery.cookie.js"></script>
```

 ###  添加回话cookie

``` stylus
$.cookie('the_cookie', 'the_value'); //有效期默认到用户关闭浏览器为止
$.cookie('the_cookie', 'the_value', { expires: 7 }); // 有效时间为 7天
$.cookie('the_cookie', 'the_value', { expires: 7, path: '/' }); //让不同页面读取cookie，需要设置路径。

```


  ### 读取cookie
 

``` stylus
$.cookie('the_cookie'); // cookie存在返回 'the_value'，不存在返回null 

```


  ### 删除cookie，通过传递null

``` stylus
$.cookie('the_cookie', null); 
$.cookie('hruserType', userType, { expires: 100, path: '/' });
$.cookie('hruserCode', userCode, { expires: 100, path: '/' });

```


