


# URL编码
在URL中只有

字母：`a -> z、A -> Z`

数字：`0 -> 9`

特殊符号：`$-_.+!*'(),`

以及某些保留字，才可以不经过编码直接用于URL。

1. 问题的由来
一般来说，URL只能使用英文字母、阿拉伯数字和某些标点符号，不能使用其他文字和符号。比如，世界上有英文字母的网址 “http://www.abc.com”，但是没有希腊字母的网址“http://www.aβγ.com”（读作阿尔法-贝塔-伽玛.com）。这是 因为网络标准RFC 1738 做了硬性规定：
这意味着，如果URL中有汉字，就必须编码后使用。但是麻烦的是，RFC 1738没有规定具体的编码方法，而是交给应用程序（浏览器）自己决定。这导致“URL编码”成为了一个混乱的领域。

下面就让我们看看，“URL编码”到底有多混乱。我会依次分析四种不同的情况，在每一种情况中，浏览器的URL编码方法都不一样。把它们的差异解释 清楚之后，我再说如何用Javascript找到一个统一的编码方法。

- ## 网址路径中包含汉子
输入网址“http://zh.wikipedia.org/wiki/春节 ”。 注意，“春节”这两个字此时是网址路径的一部分。
![photo](http://www.ruanyifeng.com/blog/upload/2010/02/bg2010021102.jpg)
查看HTTP请求的头信息，会发现IE实际查询的网址是“http://zh.wikipedia.org/wiki/%E6%98%A5%E8%8A%82 ”。 也就是说，IE自动将“春节”编码成了“%E6%98%A5%E8%8A%82”。
![photo](http://www.ruanyifeng.com/blog/upload/2010/02/bg2010021103.png)
我们知道，“春”和“节”的utf-8编码分别是“E6 98 A5”和“E8 8A 82”，因此，“%E6%98%A5%E8%8A%82”就是按照顺序，在每个字节前加上%而得到的。

- ## 查询字符串包含汉字
在IE中输入网址“http://www.baidu.com/s?wd=春节 ”。注意，“春节”这两个字此时 属于查询字符串，不属于网址路径，不要与情况1混淆。
![photo](http://www.ruanyifeng.com/blog/upload/2010/02/bg2010021104.jpg)
查看HTTP请求的头信息，会发现IE将“春节”转化成了一个乱码。
![photo](http://www.ruanyifeng.com/blog/upload/2010/02/bg2010021105.png)

切换到十六进制方式，才能清楚地看到，“春节”被转成了`B4 BA BD DA`
![photo](http://www.ruanyifeng.com/blog/upload/2010/02/bg2010021106.png)
我们知道，“春”和“节”的GB2312编码（我的操作系统“Windows XP”中文版的默认编码）分别是“B4 BA”和“BD DA”。因此，IE实际上就是将查询字符串，以GB2312编码的格式发送出去。

Firefox的处理方法，略有不同。它发送的HTTP Head是“wd=%B4%BA%BD%DA”。也就是说，同样采用GB2312编码，但是在每个字节前加上了%。

![photo](http://www.ruanyifeng.com/blog/upload/2010/02/bg2010021107.png)

结论就是，查询字符串的编码，用的是操作系统的默认编码。

- ## GET方法生成的URL包含汉字

前面说的是直接输入网址的情况，但是更常见的情况是，在已打开的网页上，直接用Get或Post方法发出HTTP请求。

根据台湾中兴大学吕瑞麟老师的试验 ，这时的编码方法由网页的编码决定，也就是由HTML源码中字符集的设定决定。

　`<meta http-equiv="Content-Type" content="text/html;charset=xxxx">`

如果上面这一行最后的charset是UTF-8，则URL就以UTF-8编码；如果是GB2312，URL就以GB2312编码。

举例来说，百度是GB2312编码，Google是UTF-8编码。因此，从它们的搜索框中搜索同一个词“春节”，生成的查询字符串是不一样的。

百度生成的是%B4%BA%BD%DA，这是GB2312编码。
![photo](http://www.ruanyifeng.com/blog/upload/2010/02/bg2010021109.jpg)
Google生成的是%E6%98%A5%E8%8A%82，这是UTF-8编码。
![photo](http://www.ruanyifeng.com/blog/upload/2010/02/bg2010021108.jpg)

所以，结论就是，** GET和POST方法的编码，用的是网页的编码 **


