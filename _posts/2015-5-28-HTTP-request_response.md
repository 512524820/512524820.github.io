---
layout: post
title: HTTP请求头与响应头
date: 2018-5-28
categories: blog
tags: [HTTP协议]
description: 。
---

# HTTP头引入

正确的设置HTTP头部信息有助于搜索引擎判断网页及提升网站访问速度。通常HTTP消息包括：客户机向服务器的请求消息和服务器向客户机的响应消息。客户端向服务器发送一个请求，请求头包含请求的方法、URI、协议版本、以及包含请求修饰符、客户信息和内容的类似于MIME的消息结构。服务器以一个状态行作为响应，相应的内容包括消息协议的版本，成功或者错误编码加上包含服务器信息、实体元信息以及可能的实体内容。
![photo](http://images.cnitblog.com/blog/641601/201410/031612320344483.jpg)

 Http协议定义了很多与服务器交互的方法，即HTTP请求的种类中，最基本的有4种，分别是**GET、POST、PUT、DELETE**。一个URL地址用于描述一个网络上的资源，而HTTP中GET、POST、PUT、 DELETE就对应着对这个资源的查、改、增、删4个操作，我们最常见的就是GET和POST了。

- GET：通过请求URI得到资源。一般用于获取/查询资源信息。
- POST：用于向服务器提交新的内容。一般用于更新资源信息。

# HTTP头信息解读
HTTP的头域包括通用头、请求头、响应头和实体头四个部分。每个头域由一个域名，冒号（:）和域值三部分组成（说白了就是键值对）。

- 通用头：是客户端和服务器都可以使用的头部，可以在客户端、服务器和其他应用程序之间提供一些非常有用的通用功能，如Date头部。
- 请求头：是请求报文特有的，它们为服务器提供了一些额外信息，比如客户端希望接收什么类型的数据，如Accept头部。
- 响应头：便于客户端提供信息，比如，客服端在与哪种类型的服务器进行交互，如Server头部。
- 实体头：指的是用于应对实体主体部分的头部，比如，可以用实体头部来说明实体主体部分的数据类型，如Content-Type头部。

# HTTP通用头
通用头域包含请求和响应消息都支持的头域，通用头域包含缓存头部Cache-Control、Pragma及信息性头部Connection、Date、Transfer-Encoding、Update、Via。
1. Cache-Control
- no-cache：指示请求或响应消息不能缓存，实际上是可以存储在本地缓存区中的，只是在与原始服务器进行新鲜度验证之前，缓存不能将其提供给客户端使用。
- no-store：缓存应该尽快从存储器中删除文档的所有痕迹，因为其中可能会包含敏感信息。
- max-age：缓存无法返回缓存时间长于max-age规定秒的文档，若不超规定秒浏览器将不会发送对应的请求到服务器，数据由缓存直接返回；超过这一时间段才进一步由服务器决定是返回新数据还是仍由缓存提供。若同时还发送了max-stale指令，则使用期可能会超过其过期时间。
- min-fresh：至少在未来规定秒内文档要保持新鲜，接受其新鲜生命期大于其当前 Age 跟 min-fresh 值之和的缓存对象。
- max-stale：指示客户端可以接收过期响应消息，如果指定max-stale消息的值，那么客户端可以接收过期但在指定值之内的响应消息。
- only-if-cached：只有当缓存中有副本存在时，客户端才会获得一份副本。
- Public：指示响应可被任何缓存区缓存，可以用缓存内容回应任何用户。
- Private：指示对于单个用户的整个或部分响应消息，不能被共享缓存处理，只能用缓存内容回应先前请求该内容的那个用户。

2. Pragma
Pragma头域用来包含实现特定的指令，最常用的是Pragma:no-cache。在HTTP/1.1协议中，它的含义和Cache- Control:no-cache相同。

3. Connection

Connection表示是否需要持久连接。如果Servlet看到这里的值为“Keep-Alive”，或者看到请求使用的是HTTP 1.1（HTTP 1.1默认进行持久连接），它就可以利用持久连接的优点，当页面包含多个元素时（例如Applet，图片），显著地减少下载所需要的时间。要实现这一点，Servlet需要在应答中发送一个Content-Length头，最简单的实现方法是：先把内容写入ByteArrayOutputStream，然后在正式写出内容之前计算它的大小。

- Close：告诉WEB服务器或者代理服务器，在完成本次请求的响应后，断开连接，不要等待本次连接的后续请求了。
- Keepalive：告诉WEB服务器或者代理服务器，在完成本次请求的响应后，保持连接，等待本次连接的后续请求。
- Keep-Alive：如果浏览器请求保持连接，则该头部表明希望 WEB 服务器保持连接多长时间（秒），如Keep-Alive：300。

4. Date

Date头域表示消息发送的时间，服务器响应中要包含这个头部，因为缓存在评估响应的新鲜度时要用到，其时间的描述格式由RFC822定义。例如，Date:Mon, 31 Dec 2001 04:25:57 GMT。Date描述的时间表示世界标准时，换算成本地时间，需要知道用户所在的时区。

5. Transfer-Encoding

WEB 服务器表明自己对本响应消息体（不是消息体里面的对象）作了怎样的编码，比如是否分块（chunked），例如：Transfer-Encoding: chunked

6. Upgrade

它可以指定另一种可能完全不同的协议，如HTTP/1.1客户端可以向服务器发送一条HTTP/1.0请求，其中包含值为“HTTP/1.1”的Update头部，这样客户端就可以测试一下服务器是否也使用HTTP/1.1了。

7. Via

列出从客户端到 OCS 或者相反方向的响应经过了哪些代理服务器，他们用什么协议（和版本）发送的请求。

当客户端请求到达第一个代理服务器时，该服务器会在自己发出的请求里面添加 Via 头部，并填上自己的相关信息，当下一个代理服务器 收到第一个代理服务器的请求时，会在自己发出的请求里面复制前一个代理服务器的请求的Via头部，并把自己的相关信息加到后面，以此类推，当 OCS 收到最后一个代理服务器的请求时，检查 Via 头部，就知道该请求所经过的路由。例如：Via：1.0 236-81.D07071953.sina.com.cn:80 (squid/2.6.STABLE13)


# HTTP请求头

请求头用于说明是谁或什么在发送请求、请求源于何处，或者客户端的喜好及能力。服务器可以根据请求头部给出的客户端信息，试着为客户端提供更好的响应。请求头域可能包含下列字段Accept、Accept-Charset、Accept- Encoding、Accept-Language、Authorization、From、Host、If-Modified-Since、If-Match、If-None-Match、If-Range、If-Range、If-Unmodified-Since、Max-Forwards、Proxy-Authorization、Range、Referer、User-Agent。对请求头域的扩展要求通讯双方都支持，如果存在不支持的请求头域，一般将会作为实体头域处理。

1. Accept

告诉WEB服务器自己接受什么介质类型，*/* 表示任何类型，type/* 表示该类型下的所有子类型，type/sub-type。

2. Accept-Charset

浏览器告诉服务器自己能接收的字符集。

3. Accept-Encoding

浏览器申明自己接收的编码方法，通常指定压缩方法，是否支持压缩，支持什么压缩方法（gzip，deflate）。

4. Accept-Language

浏览器申明自己接收的语言。语言跟字符集的区别：中文是语言，中文有多种字符集，比如big5，gb2312，gbk等等。

5. Authorization

当客户端接收到来自WEB服务器的 WWW-Authenticate 响应时，用该头部来回应自己的身份验证信息给WEB服务器。

6. If-Match

如果对象的 ETag 没有改变，其实也就意味著对象没有改变，才执行请求的动作，获取文档。

7. If-None-Match

如果对象的 ETag 改变了，其实也就意味著对象也改变了，才执行请求的动作，获取文档。

8. If-Modified-Since

如果请求的对象在该头部指定的时间之后修改了，才执行请求的动作（比如返回对象），否则返回代码304，告诉浏览器该对象没有修改。例如：If-Modified-Since：Thu, 10 Apr 2008 09:14:42 GMT

9. If-Unmodified-Since

如果请求的对象在该头部指定的时间之后没修改过，才执行请求的动作（比如返回对象）。

10. If-Range

浏览器告诉 WEB 服务器，如果我请求的对象没有改变，就把我缺少的部分给我，如果对象改变了，就把整个对象给我。浏览器通过发送请求对象的ETag 或者自己所知道的最后修改时间给 WEB 服务器，让其判断对象是否改变了。总是跟 Range 头部一起使用。

11. Range

浏览器（比如 Flashget 多线程下载时）告诉 WEB 服务器自己想取对象的哪部分。例如：Range: bytes=1173546

12. Proxy-Authenticate

代理服务器响应浏览器，要求其提供代理身份验证信息。

13. Proxy-Authorization

浏览器响应代理服务器的身份验证请求，提供自己的身份信息。

14. Host

客户端指定自己想访问的WEB服务器的域名/IP 地址和端口号。如Host：rss.sina.com.cn

15. Referer

浏览器向WEB 服务器表明自己是从哪个网页URL获得点击当前请求中的网址/URL，例如：Referer：http://www.ecdoer.com/

16. User-Agent

浏览器表明自己的身份（是哪种浏览器）。例如：Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.143 Safari/537.36

#  HTTP响应头

响应头向客户端提供一些额外信息，比如谁在发送响应、响应者的功能，甚至与响应相关的一些特殊指令。这些头部有助于客户端处理响应，并在将来发起更好的请求。响应头域包含Age、Location、Proxy-Authenticate、Public、Retry- After、Server、Vary、Warning、WWW-Authenticate。对响应头域的扩展要求通讯双方都支持，如果存在不支持的响应头域，一般将会作为实体头域处理。

1. Age

当代理服务器用自己缓存的实体去响应请求时，用该头部表明该实体从产生到现在经过多长时间了。

2. Server

WEB 服务器表明自己是什么软件及版本等信息。例如：Server：Apache/2.0.61 (Unix)

3. Accept-Ranges

WEB服务器表明自己是否接受获取其某个实体的一部分（比如文件的一部分）的请求。bytes：表示接受，none：表示不接受。

4. Vary

WEB服务器用该头部的内容告诉 Cache 服务器，在什么条件下才能用本响应所返回的对象响应后续的请求。假如源WEB服务器在接到第一个请求消息时，其响应消息的头部为：Content-Encoding: gzip; Vary: Content-Encoding，那么Cache服务器会分析后续请求消息的头部，检查其Accept-Encoding，是否跟先前响应的Vary头部值一致，即是否使用相同的内容编码方法，这样就可以防止Cache服务器用自己Cache 里面压缩后的实体响应给不具备解压能力的浏览器。例如：Vary：Accept-Encoding。

# HTTP实体头

实体头部提供了有关实体及其内容的大量信息，从有关对象类型的信息，到能够对资源使用的各种有效的请求方法。总之，实体头部可以告知接收者它在对什么进行处理。请求消息和响应消息都可以包含实体信息，实体信息一般由实体头域和实体组成。实体头域包含关于实体的原信息，实体头包括信息性头部Allow、Location，内容头部Content-Base、Content-Encoding、Content-Language、Content-Length、Content-Location、Content-MD5、Content-Range、Content-Type，缓存头部Etag、Expires、Last-Modified、extension-header。

1. Allow

服务器支持哪些请求方法（如GET、POST等）。

2. Location

表示客户应当到哪里去提取文档，用于将接收端定位到资源的位置（URL）上。Location通常不是直接设置的，而是通过HttpServletResponse的sendRedirect方法，该方法同时设置状态代码为302。

3. Content-Base

解析主体中的相对URL时使用的基础URL。

4. Content-Encoding

WEB服务器表明自己使用了什么压缩方法（gzip，deflate）压缩响应中的对象。例如：Content-Encoding：gzip

5. Content-Language

WEB 服务器告诉浏览器理解主体时最适宜使用的自然语言。

6. Content-Length

WEB服务器告诉浏览器自己响应的对象的长度或尺寸，例如：Content-Length: 26012

7. Content-Location

资源实际所处的位置。

8. Content-MD5

主体的MD5校验和。

9. Content-Range

实体头用于指定整个实体中的一部分的插入位置，他也指示了整个实体的长度。在服务器向客户返回一个部分响应，它必须描述响应覆盖的范围和整个实体长度。一般格式： Content-Range:bytes-unitSPfirst-byte-pos-last-byte-pos/entity-legth。例如，传送头500个字节次字段的形式：Content-Range:bytes0- 499/1234如果一个http消息包含此节（例如，对范围请求的响应或对一系列范围的重叠请求），Content-Range表示传送的范围，Content-Length表示实际传送的字节数。

10. Content-Type

WEB 服务器告诉浏览器自己响应的对象的类型。例如：Content-Type：application/xml

11. Etag

就是一个对象（比如URL）的标志值，就一个对象而言，比如一个html文件，如果被修改了，其Etag也会别修改，所以，ETag的作用跟Last-Modified的作用差不多，主要供WEB服务器判断一个对象是否改变了。比如前一次请求某个html文件时，获得了其 ETag，当这次又请求这个文件时，浏览器就会把先前获得ETag值发送给WEB服务器，然后WEB服务器会把这个ETag跟该文件的当前ETag进行对比，然后就知道这个文件有没有改变了。

12. Expires

WEB服务器表明该实体将在什么时候过期，对于过期了的对象，只有在跟WEB服务器验证了其有效性后，才能用来响应客户请求。是 HTTP/1.0 的头部。例如：Expires：Sat, 23 May 2009 10:02:12 GMT

13. Last-Modified

WEB服务器认为对象的最后修改时间，比如文件的最后修改时间，动态页面的最后产生时间等等。例如：Last-Modified：Tue, 06 May 2008 02:42:43 GMT


# 以百度首页为例获取请求头和响应头
1. 请求头
我们现在通过谷歌浏览器来查看一下请求头：

以打开百度首页为例，然后在谷歌浏览器中打开“工具-开发者工具”，切换到network标签， 然后刷新页面：
![photo](http://images.cnitblog.com/blog/641601/201410/031612153476585.jpg)

上图中，打开箭头处html格式的文件，显示如下：
![photo](http://images.cnitblog.com/blog/641601/201410/031612473318622.png)

上图中的Request Headers就是我们所需要的请求头。里面的内容全部是键值对。服务器拿到这些键值对后会对其进行分析。

- Host:www.baidu.com      本次请求访问的主机地址（虚拟主机名称）
- Cache-control:no-cache        设置网页缓存的使用方法
- Pragma:no-cache
- Accept:text/html,xxxxxx…..客户端可以接收的数据类型（如果内容是：*/*，表示接收所有类型）
- User-Agent:Mozilla/5.0xxxxx 主要表示客户端类型
- Accept-Encoding:gzip,deflate,sdch 浏览器能够接收的数据压缩编码方式（表示浏览器能够接收什么格式的压缩的数据）
- Accept-Language:zh-CN,zh;q=0.8 浏览器期望的接受的语言种类
- Accept-Charset: ISO-8859-1      客户端所接收的字符集编码
- If-Modified-Since: Tue, 11 Jul 2000 18:23:51 GMT   和缓存机制相关的头
- Referer: http://www.smyh.me/index.jsp   当前页面来自哪个页面（可能是由之前的页面通过超链接点进到这个页面来）,假如你通过google搜索出一个商家的广告页面，你对这个广告页面感兴趣，鼠标一点发送一个请求报文到商家的网站，这个请求报文的Referer报文头属性值就是`http://www.google.com` 
- Cookie: $Version=1; Skin=new;jsessionid=5F4771183629C9834F8382E23BE13C4C 服务端是怎么知道客户端的多个请求是隶属于一个Session呢？注意到后台的那个jsessionid=5F4771183629C9834F8382E23BE13C4C木有？原来就是通过HTTP请求报文头的Cookie属性的jsessionid的值关联起来的！
- Connection: close/Keep-Alive   请求完之后，是关闭此连接，还是继续保持连接
- Date: Tue, 11 Jul 2013 18:23:51 GMT 当前请求的时间


2. 响应头：

我们再来看一下响应头的内容：
![photo](http://images.cnitblog.com/blog/641601/201410/031614079416983.jpg)

- Location: http://www.smyh.me/index.jsp    重定向（302+Location实现重定向）  
- Server                       服务器类型
- Content-Encoding       服务器当前返回给客户端的数据压缩格式
- Content-Length          返回给客户端的数据量的大小
- Content-Language: zh-cn  语言种类
- Content-Type: text/html; charset=GB2312        返回的数据的类型、字符集编码方式
- Last-Modified             资源最后一次修改的时间（配合请求头中的If-Modified-Since+304/307实现缓存机制）
- Refresh: 1;url=http://www.it315.org 　　 隔多少秒以后，让当前页面去访问哪个地址（例如网页登陆成功后，跳回原来的界面，就是用的这个头）
- Content-Disposition: attachment;filename=aaa.zip   和下载相关，通知浏览器以附件的形式下载服务器发送过去的数据
- Transfer-Encoding: chunked  数据传输模式
- Set-Cookie:SS=Q0=5Lb_nQ; path=/search     和cookie相关的头
- ETag: W/"83794-1208174400000"       和cookie相关的头
- Expires: -1        通知浏览器是否缓存当前资源：如果这个头的值是一个以毫秒为单位值，则通知浏览器缓存资源到指定的时间点；如果值是0或-1，则通知浏览器禁止缓存
- Cache-Control: no-cache   通知浏览器是否缓存资源(响应输出到客户端后，服务端通过该报文头属告诉客户端如何控制响应内容的缓存。 下面，的设置让客户端对响应内容缓存3600秒，也即在3600秒内，如果客户再次访问该资源，直接从客户端的缓存中返回内容给客户，不要再从服务端获取（当然，这个功能是靠客户端实现的，服务端只是通过这个属性提示客户端“应该这么做”，做不做，还是决定于客户端，如果是自己宣称支持HTTP的客户端，则就应该这样实现）。 )
   - ```
     Cache-Control: max-age=3600  
     ```
- Pragma: no-cache         -- 通知浏览器是否缓存资源
注： 之所以三个头是一个功能,是因为历史原因。不同的浏览器对这三个头支持的不同,一般来说这三个头要同时使用，以确保不同的浏览器都能实现控制缓存的功能

- Connection: close/Keep-Alive   是否继续保持连接
- Date: Tue, 11 Jul 2000 18:23:51 GMT    当前响应的时间


# 响应状态码


 -   1xx：指示信息--表示请求已接收，继续处理
 - 　2xx：成功--表示请求已被成功接收、理解、接受
 - 　3xx：重定向--要完成请求必须进行更进一步的操作
 -   4xx：客户端错误--请求有语法错误或请求无法实现 **客户端错误**
 -   5xx：服务器端错误--服务器未能实现合法的请求   **服务端错误**
 
 
# 常见状态响应码说明
- 200 OK　　//客户端请求成功
- 303 see other //我把你redirect到其它的页面，目标的URL通过响应报文头的Location告诉你
   - ```
      悟空：师傅给个桃吧，走了一天了 
      唐僧：我哪有桃啊！去王母娘娘那找吧
     ```
- 304 Not Modified   //告诉客户端，你请求的这个资源至你上次取得后，并没有更改，你直接用你本地的缓存吧，我很忙哦，你能不能少来烦我啊！ 
- 400 Bad Request　　//客户端请求有语法错误，不能被服务器所理解
- 401 Unauthorized　　//请求未经授权，这个状态代码必须和WWW-Authenticate报头域一起使用 
- 403 Forbidden　　//服务器收到请求，但是拒绝提供服务
- 404 Not Found　　//请求资源不存在，eg：输入了错误的URL(如你在google上找到一个页面，点击这个链接返回404，表示这个页面已经被网站删除了)
- 500 Internal Server Error　　//服务器发生不可预期的错误(看到这个错误，你就应该查查服务端的日志了，肯定抛出了一堆异常，别睡了，起来改BUG去吧!)
- 502 Bad Gateway　　//对用户访问请求的响应超时造成的
- 503 Server Unavailable  //服务器当前不能处理客户端的请求，一段时间后,可能恢复正常
- eg：HTTP/1.1 200 OK （CRLF）


# HTTP请求报文结构体

![photo](http://dl.iteye.com/upload/attachment/0069/3487/cdc4dbbb-f98e-31d5-8270-3c37bf1c54e5.jpg)

如上图，第一部分为请求行，第二部分为请求头，第三部分为请求体

# 报文头属性是什么东西呢？我们不妨以一个小故事来说明。

快到中午了，张三丰不想去食堂吃饭：老板，我要一份[鱼香肉丝]，要12：30之前给我送过来哦，我在江湖湖公司研发部，叫张三丰。

**这里，你要[鱼香肉丝]相当于HTTP报文体，而“12：30之前送过来”，你叫“张三丰”等信息就相当于HTTP的报文头。它们是一些附属信息，帮忙你和饭店老板顺利完成这次交易。**

**请求HTTP报文和响应HTTP报文都拥有若干个报文关属性，它们是为协助客户端及服务端交易的一些附属信息。 **





