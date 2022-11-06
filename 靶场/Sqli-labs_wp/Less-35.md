测试单引号

```
?id=99'

报错
 You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '\' LIMIT 0,1' at line 1 
```

判断为无 引号，括号闭合。

因为唯一的'在报错里已经被转义了。 看不到其他引号啥的。





```
?id=99 union select 1,2,3 --+
这里order by测回显，无输出，
改用union select测试下。
```

 
