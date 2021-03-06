---
layout: post
title: Xposed+JustTrustMe突破SSL Pinning（抓app https数据包）
date: 2018-8-09
categories: blog
tags: [app抓包，https抓包]
description: 。
---

## 概述
当测试APP需要抓包时，一定遇到过burp或者fiddler抓不到包的情况，装了证书也没用，这是因为APP启用了SSL Pinning（SSL证书绑定）

## 基于https的浏览器认证原理及解决方法
在建立SSL通道的过程中，当客户端向服务端发送了链接请求后，服务器会发送自己的证书给客户端，如果客户端是浏览器，则会使用内置的CA证书去校验服务器证书是否被信任，如果不被信任，则会弹出https的告警信息，由用户自己决定是不继续访问

解决方法：用户可以主动将服务器证书导入到浏览器 的受信任区，下次打开时该服务器证书将会被自动信任

## SSL Pinning
客户端在收到服务器的证书后，对该证书进行强校验，验证该证书是不是客户端承认的证书，如果不是，则直接断开连接。
浏览器将选择权交给了用户，并允许用户导入自己的证书到受信任区。
APP里就不一样，APP是HTTPS的服务提供方自己的客户端，开发者可以将自己的服务器证书打包内置到自己的APP中，当请求服务端时先使用内置的证书校验一下服务器证书是否可发，如果不合法，直接断开。

## 破解方法
* 1 使用逆向手法破解源码，干掉SSL Pinning，但是一般这种方法比较复杂，一些APP代码经过加密混淆，费时费力
* 2 Xposed+JustTrustMe来突破
Xposed是一个框架，可以改变系统和应用程序的行为，而不接触任何APK，支持很多种模块，每个模块可以用来实现不同的功能。
JustTrustMe是一个用来禁用，绕过SSL证书检查的模块，它将APK中所有用于校验SSL证书的API都进行了HOOK，从而绕过证书检查

## 注意条件（手机系统，burp伪证书）
手机必须是经过ROOT
安装完后需要重启系统
Xposed+JustTrustMe只是解决了SSL Pinning的问题，因为通讯本身是HTTPS的，因此我们还需要安装burp的伪证书，直接导出burp证书，默认格式是crt，从手机SD卡安装即可
