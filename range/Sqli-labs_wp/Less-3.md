单引号）

首先注入 

```
?id=1'
报错
... syntax to use near ''1'') LIMIT 0,1' at line 1
可以猜测到
原查询语句为 ...where id =('what')
```

 所以注入

```
?id=1') --+ 
其他步骤与Less1 相同
```

