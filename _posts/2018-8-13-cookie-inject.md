---
layout: post
title: cookie注入原理解析
date: 2018-8-13
categories: blog
tags: [cookie注入,注入]
description: 。
---

# 什么是cookie注入
cookie注入原理和平时的注入一样，只不过将参数以cookie提交了，而一般的注入是使用get或者post方式提交。
Cookie的用途非常广泛，在网络中经常可以见到Cookie的身影。它通常被用来辨别用户身份、进行session跟踪，最典型的应用就是保存用户的账号和密码用来自动登录网站和电子商务网站中的“购物车”。

# 为什么选择要cookie注入
当服务端选择用$_GET和$_POST方式接收参数时都进行了过滤，$_COOKIE方式并没有进行严格的过滤，那么我们就可以在cookie中选择合适的参数进行绕过

# cookie注入步骤
* 1 输入带参数的网址`http://www.2cto.com /Products_show.asp?id=284`，可以访问
* 2 清空地址栏，输入`javascript:alert(document.cookie="id="+escape("284"))`;有弹框
* 3 输入`http://www.2cto.com /Products_show.asp?`查看是否可以访问，可以访问就说明程序是以reques("xx")的形式接收数据
* 4 测试在cookie中是否可以提交特殊字符
