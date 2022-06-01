# Домашняя работа к занятию "6.4. PostgreSQL"

##  Задание 1
```
maksim@MacBook-Pro Docker % sudo docker run --rm --name pg-db -e POSTGRES_PASSWORD=pgpass -ti -v Posgres_data:/var/lib/postgresql/data -v ~/Docker/pg_data:/backup postgres:13
maksim@MacBook-Pro Docker % docker exec -it pg-db bash
root@9a31c7b23b18:/# psql -h localhost -U postgres
psql (13.7 (Debian 13.7-1.pgdg110+1))
```
- \l - вывод списка БД
- \c[onnect] {[DBNAME|- USER|- HOST|- PORT|-] | conninfo} connect to new database (currently "postgres") - подключение к БД
- \dt[S+] [PATTERN] - вывод списка таблиц
- \d[S+]  NAME - вывод описания содержимого таблиц
- \q - выход из psql

## Задание 2
- Восстановление БД из бэкапа
```
root@9a31c7b23b18:/# psql -U postgres -f /backup/test_dump.sql test_database
SET
SET
SET
SET
SET
 set_config
------------

(1 row)

SET
SET
SET
SET
SET
SET
CREATE TABLE
ALTER TABLE
CREATE SEQUENCE
ALTER TABLE
ALTER SEQUENCE
ALTER TABLE
COPY 8
 setval
--------
      8
(1 row)

ALTER TABLE
```
- Сбор статистики по таблице
```
postgres=# \c test_database
You are now connected to database "test_database" as user "postgres".
test_database=# analyze verbose orders;
INFO:  analyzing "public.orders"
INFO:  "orders": scanned 1 of 1 pages, containing 8 live rows and 0 dead rows; 8 rows in sample, 8 estimated total rows
ANALYZE
```
- pg_stats
```
test_database=# select avg_width from pg_stats where tablename='orders';
 avg_width
-----------
         4
        16
         4
(3 rows)
```

## Задание 3
```
begin;
    create table database_new (
        id integer,
        title varchar(80),
        price integer) partition by range(price);
    create table orders_priceless partition of database_new for values from (0) to (499);
    create table orders_pricemore partition of database_new for values from (499) to (99999);
    insert into databse_new (id, title, price) select * from orders;
commit;
```
Думаю, что могли исключить исключить "ручное" разбиение, если на первоначальном этапе применили Секционирование таблицы.

## Задание 4
```
root@9a31c7b23b18:/# pg_dump -U postgres -d test_database > backup/dump_3105.sql
```
Для уникальности можно добавить индекс.
