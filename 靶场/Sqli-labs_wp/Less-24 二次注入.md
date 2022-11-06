二次注入

先注册个账号

admin'# 

密码随便填。





猜测源码

updata user set password='new_pass' where username='***admin'#*** and password=''

这样，我们修改用户admin‘#的密码时，就会修改掉admin的密码，获得管理员账号

 
