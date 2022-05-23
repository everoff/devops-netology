## Задача 1

Привожу docker-compose манифест, считаю, что данный метод создания позволяет соблюсти идемпотентность.

```
version: "3.9"

volumes:
  pgsql_db:
  pgsql_db_backup:

services:
    postgres:
      image: postgres:12
      container_name: postgres
      environment:
        - PGDATA=/var/lib/postgresql/data/pgdata
        - POSTGRES_PASSWORD=pass!
      volumes:
        - pgsql_db:/var/lib/postgresql/data
        - pgsql_db_backup:/backup
        - ./config:/docker-entrypoint-initdb.d
```
##  Задача 2

На сайте репозитория Postgres увидел интересный функционал для иницииализации БД при первом запуске контейнера, решил попробовать и указал в файле скрипта команды для выполнения данного задания.

```
CREATE DATABASE test_db
        TEMPLATE template0
        ENCODING 'UTF-8';
CREATE USER "test-admin-user";
CREATE USER "test-simple-user";
CREATE TABLE orders (
        id      serial PRIMARY KEY,
        order_name      varchar(25) NOT NULL CHECK (order_name <> ''),
        price   integer NOT NULL CHECK (price > 0)
);
CREATE TABLE clients (
        id      serial PRIMARY KEY,
        last_name       varchar(45) NOT NULL CHECK (last_name <> ''),
        country varchar(35) NOT NULL CHECK (country <> ''),
        order_number    integer REFERENCES orders
);
CREATE INDEX country_index ON clients(country);
GRANT ALL ON ALL TABLES IN SCHEMA "public" TO "test-admin-user";
GRANT SELECT, INSERT, UPDATE, DELETE
        ON ALL TABLES IN SCHEMA "public"
        TO "test-simple-user";
```

- Список БД
```
postgres=# \l
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges
-----------+----------+----------+------------+------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 test_db   | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
(4 rows)
```

- Описание таблиц
```
postgres=# \d+ orders
                                                         Table "public.orders"
   Column   |         Type          | Collation | Nullable |              Default               | Storage  | Stats target | Description
------------+-----------------------+-----------+----------+------------------------------------+----------+--------------+-------------
 id         | integer               |           | not null | nextval('orders_id_seq'::regclass) | plain    |              |
 order_name | character varying(25) |           | not null |                                    | extended |              |
 price      | integer               |           | not null |                                    | plain    |              |
Indexes:
    "orders_pkey" PRIMARY KEY, btree (id)
Check constraints:
    "orders_order_name_check" CHECK (order_name::text <> ''::text)
    "orders_price_check" CHECK (price > 0)
Referenced by:
    TABLE "clients" CONSTRAINT "clients_order_number_fkey" FOREIGN KEY (order_number) REFERENCES orders(id)
Access method: heap

postgres=# \d+ clients
                                                          Table "public.clients"
    Column    |         Type          | Collation | Nullable |               Default               | Storage  | Stats target | Description
--------------+-----------------------+-----------+----------+-------------------------------------+----------+--------------+-------------
 id           | integer               |           | not null | nextval('clients_id_seq'::regclass) | plain    |              |
 last_name    | character varying(45) |           | not null |                                     | extended |              |
 country      | character varying(35) |           | not null |                                     | extended |              |
 order_number | integer               |           |          |                                     | plain    |              |
Indexes:
    "clients_pkey" PRIMARY KEY, btree (id)
    "country_index" btree (country)
Check constraints:
    "clients_country_check" CHECK (country::text <> ''::text)
    "clients_last_name_check" CHECK (last_name::text <> ''::text)
Foreign-key constraints:
    "clients_order_number_fkey" FOREIGN KEY (order_number) REFERENCES orders(id)
Access method: heap
```

- Список пользователей
```
postgres=# select * from information_schema.role_table_grants where table_name='orders';
 grantor  |     grantee      | table_catalog | table_schema | table_name | privilege_type | is_grantable | with_hierar
chy
----------+------------------+---------------+--------------+------------+----------------+--------------+------------
----
 postgres | postgres         | postgres      | public       | orders     | INSERT         | YES          | NO
 postgres | postgres         | postgres      | public       | orders     | SELECT         | YES          | YES
 postgres | postgres         | postgres      | public       | orders     | UPDATE         | YES          | NO
 postgres | postgres         | postgres      | public       | orders     | DELETE         | YES          | NO
 postgres | postgres         | postgres      | public       | orders     | TRUNCATE       | YES          | NO
 postgres | postgres         | postgres      | public       | orders     | REFERENCES     | YES          | NO
 postgres | postgres         | postgres      | public       | orders     | TRIGGER        | YES          | NO
 postgres | test-admin-user  | postgres      | public       | orders     | INSERT         | NO           | NO
 postgres | test-admin-user  | postgres      | public       | orders     | SELECT         | NO           | YES
 postgres | test-admin-user  | postgres      | public       | orders     | UPDATE         | NO           | NO
 postgres | test-admin-user  | postgres      | public       | orders     | DELETE         | NO           | NO
 postgres | test-admin-user  | postgres      | public       | orders     | TRUNCATE       | NO           | NO
 postgres | test-admin-user  | postgres      | public       | orders     | REFERENCES     | NO           | NO
 postgres | test-admin-user  | postgres      | public       | orders     | TRIGGER        | NO           | NO
 postgres | test-simple-user | postgres      | public       | orders     | INSERT         | NO           | NO
 postgres | test-simple-user | postgres      | public       | orders     | SELECT         | NO           | YES
 postgres | test-simple-user | postgres      | public       | orders     | UPDATE         | NO           | NO
 postgres | test-simple-user | postgres      | public       | orders     | DELETE         | NO           | NO
(18 rows)

postgres=# select * from information_schema.role_table_grants where table_name='clients';
 grantor  |     grantee      | table_catalog | table_schema | table_name | privilege_type | is_grantable | with_hierarchy
----------+------------------+---------------+--------------+------------+----------------+--------------+----------------
 postgres | postgres         | postgres      | public       | clients    | INSERT         | YES          | NO
 postgres | postgres         | postgres      | public       | clients    | SELECT         | YES          | YES
 postgres | postgres         | postgres      | public       | clients    | UPDATE         | YES          | NO
 postgres | postgres         | postgres      | public       | clients    | DELETE         | YES          | NO
 postgres | postgres         | postgres      | public       | clients    | TRUNCATE       | YES          | NO
 postgres | postgres         | postgres      | public       | clients    | REFERENCES     | YES          | NO
 postgres | postgres         | postgres      | public       | clients    | TRIGGER        | YES          | NO
 postgres | test-admin-user  | postgres      | public       | clients    | INSERT         | NO           | NO
 postgres | test-admin-user  | postgres      | public       | clients    | SELECT         | NO           | YES
 postgres | test-admin-user  | postgres      | public       | clients    | UPDATE         | NO           | NO
 postgres | test-admin-user  | postgres      | public       | clients    | DELETE         | NO           | NO
 postgres | test-admin-user  | postgres      | public       | clients    | TRUNCATE       | NO           | NO
 postgres | test-admin-user  | postgres      | public       | clients    | REFERENCES     | NO           | NO
 postgres | test-admin-user  | postgres      | public       | clients    | TRIGGER        | NO           | NO
 postgres | test-simple-user | postgres      | public       | clients    | INSERT         | NO           | NO
 postgres | test-simple-user | postgres      | public       | clients    | SELECT         | NO           | YES
 postgres | test-simple-user | postgres      | public       | clients    | UPDATE         | NO           | NO
 postgres | test-simple-user | postgres      | public       | clients    | DELETE         | NO           | NO
(18 rows)
```

## Задача 3
- Заполнение таблиц
```
postgres=#
insert into orders VALUES (1, 'Шоколад', 10), (2, 'Принтер', 3000), (3, 'Книга', 500), (4, 'Монитор', 7000), (5, 'Гитара', 4000);
INSERT 0 5
postgres=# insert into clients VALUES (1, 'Иванов Иван Иванович', 'USA'), (2, 'Петров Петр Петрович', 'Canada'), (3, 'Иоганн Себастьян Бах', 'Japan'), (4, 'Ронни Джеймс Дио', 'Russia'), (5, 'Ritchie Blackmore', 'Russia');
INSERT 0 5
```
- Вывод информации
```
postgres=# select count (*) from orders;
 count
-------
     5
(1 row)

postgres=# select count (*) from clients;
 count
-------
     5
(1 row)

postgres=# SELECT * FROM orders;
 id | order_name | price
----+------------+-------
  1 | Шоколад    |    10
  2 | Принтер    |  3000
  3 | Книга      |   500
  4 | Монитор    |  7000
  5 | Гитара     |  4000
(5 rows)

postgres=# SELECT * FROM clients;
 id |      last_name       | country | order_number
----+----------------------+---------+--------------
  1 | Иванов Иван Иванович | USA     |
  2 | Петров Петр Петрович | Canada  |
  3 | Иоганн Себастьян Бах | Japan   |
  4 | Ронни Джеймс Дио     | Russia  |
  5 | Ritchie Blackmore    | Russia  |
(5 rows)
```

## Задача 4
```
postgres=#
postgres=# UPDATE clients SET order_number=3 WHERE id=1;
UPDATE 1
postgres=# UPDATE clients SET order_number=4 WHERE id=2;
UPDATE 1
postgres=# UPDATE clients SET order_number=5 WHERE id=3;
UPDATE 1

postgres=# SELECT * FROM clients;
 id |      last_name       | country | order_number
----+----------------------+---------+--------------
  4 | Ронни Джеймс Дио     | Russia  |
  5 | Ritchie Blackmore    | Russia  |
  1 | Иванов Иван Иванович | USA     |            3
  2 | Петров Петр Петрович | Canada  |            4
  3 | Иоганн Себастьян Бах | Japan   |            5
(5 rows)

postgres=#
postgres=# SELECT * FROM clients WHERE order_number IS NOT NULL;
 id |      last_name       | country | order_number
----+----------------------+---------+--------------
  1 | Иванов Иван Иванович | USA     |            3
  2 | Петров Петр Петрович | Canada  |            4
  3 | Иоганн Себастьян Бах | Japan   |            5
(3 rows)
```

## Задача 5

```
postgres=# EXPLAIN SELECT * FROM clients WHERE order_number IS NOT NULL;
                         QUERY PLAN
------------------------------------------------------------
 Seq Scan on clients  (cost=0.00..13.50 rows=348 width=204)
   Filter: (order_number IS NOT NULL)
(2 rows)
```

## Задача 6

- Создать бэкап БД test_db
```
root@5c3b8dbb1ab9:/# pg_dump -U postgres test_db -f backup/db_test.sql
root@5c3b8dbb1ab9:/# ls backup/
db_test.sql
```

- Остановить контейнер и создайте новый пустой
```
maksim@MacBook-Pro Docker % docker stop postgres
postgres
maksim@MacBook-Pro Docker % sudo docker run  --name postgres2 -e POSTGRES_PASSWORD=pass!  -e PGDATA=/var/lib/postgresql/data/pgdata -v docker_pgsql_db_backup:/backup postgres:12
```

- Восстановить БД test_db в новый контейнер
```
root@b3eb26d520ff:/# psql -U postgres -f /backup/
backup_all   db_test.sql
root@b3eb26d520ff:/# psql -U postgres -f /backup/backup_all
postgres=# \l
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges
-----------+----------+----------+------------+------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 test_db   | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
(4 rows)

postgres=# \dt
          List of relations
 Schema |  Name   | Type  |  Owner
--------+---------+-------+----------
 public | clients | table | postgres
 public | orders  | table | postgres
(2 rows)
postgres=# SELECT * FROM clients;
 id |      last_name       | country | order_number
----+----------------------+---------+--------------
  4 | Ронни Джеймс Дио     | Russia  |
  5 | Ritchie Blackmore    | Russia  |
  1 | Иванов Иван Иванович | USA     |            3
  2 | Петров Петр Петрович | Canada  |            4
  3 | Иоганн Себастьян Бах | Japan   |            5
(5 rows)
```
