这题

有个友情链接



看来就是要插入到href里



先测试

```
onfocus <script> <a href=javascript:alert('xss')>
```

用开发者工具查看，发现，href那里提示非法的url路径。



看网上帖子说，可以在后面添加一个http://www.baidu.com 来通过检测



在http前面再添加//来达成注释效果



最后把javascript:alert('xss')

进行unicode编码



过关
