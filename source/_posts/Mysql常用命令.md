---
title: Mysql常用命令
date: 2018-06-23 14:34:27
tags:
 - Mysql
 - 常用命令
categories:
 - Mysql
---

# 常用命令
查看线程:   
```
select id, db, user, host, command, time, state, info
from information_schema.processlist
where db like 'resource_01'
order by time desc ;
```


UNION ALL: 将两张表的数据拼装到一起返回.

联表查询: 
```sql
SELECT tb_students.id, tb_students.stuName, tb_profession.proName AS stuProfession FROM tb_students, tb_profession WHERE 
tb_students.stuProfession = tb_profession.id ORDER BY tb_students.id 


SELECT tb_profession.*, COUNT(*) AS proCount
        FROM tb_students
        INNER JOIN tb_profession
        ON tb_profession.id = tb_students.stuProfession
        GROUP BY tb_profession.id
```