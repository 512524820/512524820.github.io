


通常，我们的Tcp服务器会放在IDC机房的某一个或几个防火墙后面，客户端与服务器之间的TCP连接会经过防火墙中转，如下图所示：
![photo](http://images.cnblogs.com/cnblogs_com/zhuweisky/TcpFirewall.JPG)
在这种情况下，有一点特别需要注意：当Firewall与Server之间的Tcp连接在一段时间（如10分钟）内没有任何应用层的消息经过时，
Firewall可能会主动断开与Server之间的Tcp连接，但是Client与Firewall之间的连接一直是有效的。这种情况下，Server以为Client已经断开连接了，而Client却以为自己和Server之间的连接还保持着。

*相关链接：*
[由防火墙导致的数据库空闲连接断开问题](http://blog.csdn.net/haozhongjun/article/details/78770073)
[tcp和udp包穿透防火墙-Httptunnel](http://blog.csdn.net/educast/article/details/39472227)
[黑客突破TCP-IP过滤/防火墙进入内网](http://blog.csdn.net/badtoperfect/article/details/6297349)
