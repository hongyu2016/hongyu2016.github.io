---
title: ubantu apt-get install方式安装mysql
abbrlink: 13666
date: 2019-05-15 14:41:02
tags: linux
categories: linux
---

# 在ubantu上安装mysql并不难，主要是记录下安装数据库时居然不像windows那样选择安装位置，设置用户和密码等

1. `sudo apt-get install mysql-server` 安装mysql核心服务
2. `sudo apt-get install mysql-server` 安装mysql客户端

安装完成后我们需要去mysql目录查看用户名和密码
`sudo vim /etc/mysql/debain.cnf`
```
[client]
host     = localhost
user     = debian-sys-maint
password = wrMqzOiffsyheEe1
socket   = /var/run/mysqld/mysqld.sock
[mysql_upgrade]
host     = localhost
user     = debian-sys-maint
password = wrMqzOiffsyheEe1
socket   = /var/run/mysqld/mysqld.sock

```
我们用debian-sys-maint用户来登陆
`mysql -u debian-sys-maint -p`
输入密码：`wrMqzOiffsyheEe1`
然后修改密码：
```
update mysql.user set authentication_string=PASSWORD('root'), plugin='mysql_native_password' where user='root';
```
<font color="red">
注意，authentication_string是密码 ，plugin是验证方式，这两个一定要同时改。从mysql5.7开始root的默认验证方式是auth_socket
</font>
可以输入 ` select user, plugin from mysql.user;` 查看到以下内容：
```
+------------------+-----------------------+
| user             | plugin                |
+------------------+-----------------------+
| root             | auth_socket           |
| mysql.session    | mysql_native_password |
| mysql.sys        | mysql_native_password |
| debian-sys-maint | mysql_native_password |
+------------------+-----------------------+
4 rows in set (0.02 sec)

```
修改后的：
```
+------------------+-----------------------+
| user             | plugin                |
+------------------+-----------------------+
| root             | mysql_native_password |
| mysql.session    | mysql_native_password |
| mysql.sys        | mysql_native_password |
| debian-sys-maint | mysql_native_password |
+------------------+-----------------------+
4 rows in set (0.02 sec)
```
mysql_native_password这种系统root用户登录时才可以登录数据库的root用户。所以需要把auth_socket改为mysql_native_password，才可以不受系统用户限制。

然后`exit;`推出mysql。`sudo service mysql restart` 重启mysql服务

