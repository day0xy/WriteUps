```
admin
1


出现user-agent
```



```
admin
1'
文字变红，显示失败。
```





看其他人的帖子，说是username和password都不能注入

尝试从user-agent注入



用burp抓包

```
admin
1

修改user-agent
' and extractvalue(1,concat(0x7e,(select database()),0x7e)) and '1'='1

出现xpath error
爆出security数据库

后面为一般步骤。
```

