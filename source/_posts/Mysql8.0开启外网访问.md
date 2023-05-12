---
title: Mysql 8.0 开启外网访问
date: 2023-1-1 10:27:17
tags:
 - Mysql
 - 配置
categories:
 - Mysql
---

# Mysql 8.0 开启外网访问

## 配置修改

```bash
# vim /etc/mysql/mysql.conf.d/mysqld.cnf
# 注释掉这两行
#bind-address           = 127.0.0.1
#mysqlx-bind-address    = 127.0.0.1
```

## 权限设置

mysql 8.0 更换了默认认证插件

```bash
mysql -uroot
USE mysql;
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'passw';
grant all privileges on *.* to 'root'@'%';
flush privileges;
```

## 参数优化

```bash
vim /etc/mysql/mysql.conf.d/mysqld.cnf

# 总内存的50%到75%
innodb_buffer_pool_size = 4G
# 1G 1个
innodb_buffer_pool_instances = 4
```

