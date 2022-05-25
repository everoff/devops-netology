# Домашняя работа к занятию "6.3. MySQL"

## Задача 1
- поднятие контейнера
```
maksim@MacBook-Pro Docker % sudo docker run  --name mysql -e MYSQL_ROOT_PASSWORD=mysql -p 3306:3306 -v ~/Docker/test-data/:/backup -v mysql_dv_data:/etc/mysql mysql:8
```
- вывод статуса БД
```
mysql> \s
--------------
mysql  Ver 8.0.29 for Linux on x86_64 (MySQL Community Server - GPL)

Connection id:		34
Current database:	test_db
Current user:		root@localhost
SSL:			Not in use
Current pager:		stdout
Using outfile:		''
Using delimiter:	;
Server version:		8.0.29 MySQL Community Server - GPL
Protocol version:	10
Connection:		Localhost via UNIX socket
Server characterset:	utf8mb4
Db     characterset:	utf8mb3
Client characterset:	latin1
Conn.  characterset:	latin1
UNIX socket:		/var/run/mysqld/mysqld.sock
Binary data as:		Hexadecimal
Uptime:			43 min 34 sec

Threads: 2  Questions: 146  Slow queries: 0  Opens: 180  Flush tables: 3  Open tables: 97  Queries per second avg: 0.055
```
- вывод перечня таблиц
```
mysql> show tables;
+-------------------+
| Tables_in_test_db |
+-------------------+
| orders            |
+-------------------+
1 row in set (0.00 sec)
```
- вывод количества записей с price>300
```
mysql> select count(*) from orders where price >300;
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)
```

## Задача 2
```
mysql> create user 'test'@'localhost'
    -> identified with mysql_native_password by 'test_pass'
    -> password expire interval 180 day
    -> failed_login_attemps 3
    -> with max_queries_per_hour 100
    -> attribute '{"lname": "Pretty","fname": "James"}';
mysql> GRANT Select ON test_db.orders TO 'test'@'localhost';
mysql> select * from INFORMATION_SCHEMA.USER_ATTRIBUTEs where user = 'test';
+------+-----------+---------------------------------------+
| USER | HOST      | ATTRIBUTE                             |
+------+-----------+---------------------------------------+
| test | localhost | {"fname": "James", "lname": "Pretty"} |
+------+-----------+---------------------------------------+
1 row in set (0.00 sec)
```

## Задача 3
```
mysql> show table status where name = 'orders';
+--------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+--------------------+----------+----------------+---------+
| Name   | Engine | Version | Row_format | Rows | Avg_row_length | Data_length | Max_data_length | Index_length | Data_free | Auto_increment | Create_time         | Update_time         | Check_time | Collation          | Checksum | Create_options | Comment |
+--------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+--------------------+----------+----------------+---------+
| orders | InnoDB |      10 | Dynamic    |    5 |           3276 |       16384 |               0 |            0 |         0 |              6 | 2022-05-25 11:00:09 | 2022-05-25 11:00:09 | NULL       | utf8mb4_0900_ai_ci |     NULL |                |         |
+--------+--------+---------+------------+------+----------------+-------------+-----------------+--------------+-----------+----------------+---------------------+---------------------+------------+--------------------+----------+----------------+---------+
1 row in set (0.01 sec)

mysql> show profiles;
+----------+------------+------------------------------------------------------------------------------------------------------------------------------------+
| Query_ID | Duration   | Query                                                                                                                              |
+----------+------------+------------------------------------------------------------------------------------------------------------------------------------+
|        5 | 0.04821500 | alter table orders engine = MyISAM                                                                                                 |
|        6 | 0.06187900 | alter table orders engine = InnoDB                                                                                                 |
+----------+------------+------------------------------------------------------------------------------------------------------------------------------------+
```

## Задача 4

```
[mysqld]
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
datadir         = /var/lib/mysql
secure-file-priv= NULL

innodb_flush_log_at_trx_commit = 0
innodb_file_per_table = 1
autocommit = 0
innodb_log_buffer_size	= 1M
key_buffer_size = 2458М
max_binlog_size	= 100M
```
