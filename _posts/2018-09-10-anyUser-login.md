---
layout: post
title: 任意用户登录之APP抓包
date: 2018-09-10
categories: blog
tags: [任意用户登录,APP,漏洞]
description: 。
---


# 安卓APP测试

安卓APP测试大部分还是集中在网络通信上。因此在对安卓APP测试中，网络抓包也是很重要的，一般来说，APP采用HTTP协议的最多，还有Websocket，Socket等。针对HTTP和Websocket，可以用Burp Suite工具进行抓包。

本文讲解到的是如何使用Burpsuite对手机App抓包，以及对某一款社交分享APP的任意用户登录漏洞实例讲解

# 环境配置
## 准备工具
-  安卓手机
- WIFI
- 电脑
- Burp Suite

## 手机端配置

1. 让安卓手机和电脑连入同一个WIFI，即是在同一个无线局域网环境下。

![photo](https://bbs.ichunqiu.com/data/attachment/forum/201608/01/150825eq0suud891sm1mz3.png)

2. 查看电脑的本地IP地址，命令行下输入ipconfig。

![photo](https://bbs.ichunqiu.com/data/attachment/forum/201608/01/171027z3tck1op1keetr13.png)

无线局域网的IP地址

3. 设置手机代理IP，这里以小米系统为例
![photo](https://bbs.ichunqiu.com/data/attachment/forum/201608/01/171121tdwbw26az8awft8b.png)

代理设置为“手动”，设置主机名为电脑无线局域网IP地址，端口可以随便设置，其他的设置可以默认。

## 电脑端配置

1. 打开Burpsuite，设置Burp suite代理服务器，点击“Proxy”然后“Options”。
![photo](https://bbs.ichunqiu.com/data/attachment/forum/201608/01/171220iltnoso4vljtp7pp.png)

   点击“add”添加和手机一样的IP地址和端口，要跟手机上设置的那个端口一致。记得勾上前面的括号。

2. 设置好了后，在“Proxy”：〉“Intercept”下开启“Intercept is on”就可以对安卓APP的网络数据进行抓包了。
![photo](https://bbs.ichunqiu.com/data/attachment/forum/201608/01/172606xz2l2k10mrtrx1oo.png)


# 实例漏洞

## 某款社交分享APP的任意用户登录漏洞实例

1. 登录时使用Burp suite抓包，登录包和返回的信息。

![photo](https://bbs.ichunqiu.com/data/attachment/forum/201608/01/223325qlt4j5jthb00txjg.png)

2. 登录时修改主播ID为其他主播ID。

![photo](https://bbs.ichunqiu.com/data/attachment/forum/201608/01/224302etdapktxdd0a66vl.png)

3. 修改后主播ID后点击“Forward”

![phto](https://bbs.ichunqiu.com/data/attachment/forum/201608/01/224944j9jlj8n3nj266oqf.png)

登录成功（某知名女主播账号
