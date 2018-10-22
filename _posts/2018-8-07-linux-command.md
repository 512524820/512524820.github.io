---
layout: post
title: linux常用命令
date: 2018-8-07
categories: blog
tags: [命令,linux]
description: 。
---

## 文件类型
file ./ 
```java
bandit4@bandit:~/inhere$ file ./*
./-file00: data
./-file01: data
./-file02: data
./-file03: data
./-file04: data
./-file05: data
./-file06: data
./-file07: ASCII text
./-file08: data
./-file09: data
```

## 查看指定文件
find. -type f -size 1033c
f:普通文件
size:文件大小
c:byte
```java
bandit5@bandit:~/inhere$ ls -l
total 80
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere00
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere01
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere02
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere03
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere04
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere05
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere06
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere07
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere08
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere09
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere10
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere11
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere12
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere13
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere14
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere15
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere16
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere17
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere18
drwxr-x--- 2root bandit5 4096Dec 2814:34 maybehere19
bandit5@bandit:~/inhere$ find. -type f -size 1033c
./maybehere07/.file2
```

## 查找指定用户、所属用户组
find / -group bandit6 -user bandit7 -size 33c 2>/dev/null
group:用户组
user:用户
2:错误输出
/dev/null:linux黑洞
```java
bandit6@bandit:~$ find / -group bandit6 -user bandit7 -size 33c 2>/dev/null
/var/lib/dpkg/info/bandit7.password
bandit6@bandit:~$ ls -ltr /var/lib/dpkg/info/bandit7.password
-rw-r----- 1 bandit7 bandit6 33 Sep 23 21:10 /var/lib/dpkg/info/bandit7.password
bandit6@bandit:~$
```

## 排序筛选文本
sort data.txt | uniq -u
sort:排序文本内容
uniq:uniq命令用于报告或忽略文件中的重复行，一般与sort命令结合使用
u:仅显示出一次的行列
```java
bandit8@bandit:~$ sort data.txt
zOWUUtQdwJL207BcRdVbWbco5WnlxM7U
zOWUUtQdwJL207BcRdVbWbco5WnlxM7U
zOWUUtQdwJL207BcRdVbWbco5WnlxM7U
zOWUUtQdwJL207BcRdVbWbco5WnlxM7U
zOWUUtQdwJL207BcRdVbWbco5WnlxM7U
zOWUUtQdwJL207BcRdVbWbco5WnlxM7U
zOWUUtQdwJL207BcRdVbWbco5WnlxM7U
zOWUUtQdwJL207BcRdVbWbco5WnlxM7U
zOWUUtQdwJL207BcRdVbWbco5WnlxM7U
zYO5VFsHM7EpKMGeFgJICTfsLHjtUYgR
zYO5VFsHM7EpKMGeFgJICTfsLHjtUYgR
zYO5VFsHM7EpKMGeFgJICTfsLHjtUYgR
zYO5VFsHM7EpKMGeFgJICTfsLHjtUYgR
zYO5VFsHM7EpKMGeFgJICTfsLHjtUYgR
zYO5VFsHM7EpKMGeFgJICTfsLHjtUYgR
zYO5VFsHM7EpKMGeFgJICTfsLHjtUYgR
zYO5VFsHM7EpKMGeFgJICTfsLHjtUYgR
zYO5VFsHM7EpKMGeFgJICTfsLHjtUYgR
zYO5VFsHM7EpKMGeFgJICTfsLHjtUYgR
bandit8@bandit:~$ sort data.txt | uniq -u
UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
bandit8@bandit:~$
```

##从文件中筛选可打印的字符
strings data.txt
strings:strings 是在文件中查找可打印字符串并输出长度为 4 个或更多的字符串
```java
bandit9@bandit:~$ file data.txt
data.txt: data
bandit9@bandit:~$
bandit9@bandit:~$ strings data.txt | grep ==
==========theP`
==========password
L==========isA
==========truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk
```





 

