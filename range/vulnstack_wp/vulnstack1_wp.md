### secure_file_priv

```
这是mysql一个新特性
```

查询一下

```
show variables like '%secure%';
```

出现如下

|                  |       |
| :--------------- | :---- |
| Variable_name    | Value |
| secure_auth      | OFF   |
| secure_file_priv | NULL  |

```
secure_file_priv
为null		表示限制mysql的导入与导出
为目录		   表示只允许在该目录下导入导出
无值			表示不设置限制
```





### mysql模糊查询

``` 
[语句] like '字符串'
show variables like 'general_log'
show variables like '%general%'
select * from user like 'jac%'		查询一jac开头的用户

还可以用_等作为通配符
```

