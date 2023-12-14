---
title: Mysql配置远程登录
date: 2023-12-12 14:20:46
categories: 运维Linux
---
# Mysql配置远程登录

1. 选择mysql库

~~~bash
use mysql;
~~~

2. 修改访问权限（二选一）

直接修改user表

~~~bash
update user set host='%' where user='root';
~~~

设置访问权限语句

```bash
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION;
```

3. 刷新访问权限

```bash
flush privileges;
```

4. 查看用户表是否添加成功

```bash
select user, host from user where user='root';
```

![image-20231023152248053](images/image-20231023152248053.png)
