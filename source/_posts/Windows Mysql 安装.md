---
title: Windows Mysql 安装
date: 2022-12-23 14:11:01
tags:
 - Windows
 - Mysql
categories:
 - Windows
---

# mysql 安装

## 基础配置
1. 官网下载: [Download MySQL Community Server](https://dev.mysql.com/downloads/mysql/), 选好版本点击 `download`, 新页面点击`No thanks, just start my download.`, 不注册直接下载
2. 下载完成后解压, 进入 `mysql` 根目录创建配置文件: `my.ini`, 现在都没有默认的配置文件
    ```
    [mysql]
    # 设置mysql客户端默认字符集
    default-character-set=utf8mb4
    [mysqld]
    #设置3306端口
    port=3306
    # 设置mysql的安装目录
    basedir=C:\\Users\\xiaow\\Documents\\mysql-5.7.27-winx64
    datadir=C:\\Users\\xiaow\\Documents\\mysql-5.7.27-winx64\\data
    # 允许最大连接数
    max_connections=200
    # 服务端使用的字符集默认为8比特编码的latin1字符集
    character-set-server=utf8mb4
    # 创建新表时将使用的默认存储引擎
    default-storage-engine=INNODB
    ```
3. 配置环境变量  
    1. 在用户环境变量中新建, 变量名为`MYSQL`, 变量值为`C:\Users\xiaow\Documents\mysql-5.7.27-winx64\` (mysql目录)
    2. 在用户变量的`PATH`中添加 `%MySQL%\bin`

## 安装配置服务
1. 管理员模式运行 `cmd`, 进入 `mysql` 目录下的 `bin` 目录, 依次执行
    1. `C:\Users\xiaow\Documents\mysql-5.7.27-winx64\bin\`
    2. 安装服务: `mysqld -install` | 卸载服务: `mysqld -remove`
    3. 初始化 `mysql` : `mysqld --initialize` , 注意不要手动创建 `data` 
    4. 启动: `net start mysql` | 关闭: `net stop mysql`
2. 设置密码
    1. 停止 `mysql`: `net stop mysql`
    2. 启动无密码模式 `mysql` : `mysqld --skip-grant-tables`, 注意执行完毕会无法输入任何字符
    3. 开一个新的 `cmd` 窗口, 无密码登陆: `mysql -u root`
    4. 刷新权限: `flush privileges;`
    5. 设置密码: `grant all privileges on *.* to 'root'@'localhost' identified by 'password' with grant option;`
    6. 刷新权限更新 `root` 密码: `flush privileges;`
    7. 退出: `exit`
    8. 返回上一个 `cmd`, `ctrl+c` 停止无密码模式, 正常启动即可
    9. 登陆 `mysql` : `mysql -u root -h 127.0.0.1 -ppassword`

