---
title: Http详解
date: 2022-6-21
author: penghaowen
---

## Http Method详解
---

### 背景

Http Method是客户端与服务端交互的一种请求方式，常用的Method有两种，分别是GET和POST。
在早期网页开发中，前段和后端还没有分离，服务器代码和网页代码糅合在一起，与服务器交互主要是通过html的form表单来进行的，像是这样的：

```html
<form action="/login" method="get"> <!--method="post"-->
    <input id="username" type="text" name="username"> <br>
    <input id="password" type="password" name="password"> <br>
    <input type="submit" value="提交">
</form>
```

这是一个用户登录的表单，通过在“input”标签中输入用户名和密码，然后点击提交之后，表单会发送数据到服务器，然后服务器验证用户名和密码完成登录。点击提交按钮之后我们观察浏览器的URL地址栏，首先是“method=get”时的地址栏：

`http://xxxx/login?username=admin&password=199798`

再看“method=post”时的地址栏：

`http://xxxx/login`

这是“get”与“post”最直观的区别。

### Get与Post

上面我们做了个测试发现了这两种请求方式最只管的区别：

Get会将表单中的数据拼接到url后面，直接通过url明文传输，哪怕输入的是密码！因此Get是不能用于传输敏感数据的。同时url的长度是有限制的，常用的Chrome浏览器最大支持8182个字符的url，也就是说url能承载的数据量是有限的；

Post会将表单中的数据放到自己的Request Body中进行传输，他对用户是不可见的。同时能承载的数据量也比Get更大，甚至能够用于上传文件！

但这并不是说Get完全没有优点，它足够简单！

### Postman

后来，网页开发逐渐发展为前后端分离的模式：服务端只负责处理数据，并将处理后的结果传输给前端。Web端只专注于处理显示逻辑和动画、特效等。这样做的好处自然就是各自只专注与自身职责，前端与后端解耦，不再糅杂在一起，调试变得前所未有的方便。另外，后端的接口应用对象不再只局限于Web端，只要是兼容Json的终端都能够应用，因此，一套接口，多端使用的模式它来了！

Postman就是专门针对后端接口的一款专业测试软件，我们可以通过Postman上的结构来分析Get和Post的差异：
