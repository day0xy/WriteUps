PHP htmlspecialchars() 

```
把预定义的 < >号都转化为html实体
意思就是，标签会被打印出来。
```



< >号都被转义，

用事件和js伪协议。

```
 ‘ onclick=javascript:alert('xss') '
 
 value= '  ' onclick=javascript:alert('xss') ' '
 这样 前后都为空引号，中间的onclick就会被执行。
```

