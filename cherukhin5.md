Список имеющихся баз показывает команда \l:

\l
                                                             List of databases
         Name         |  Owner   | Encoding | Locale Provider |   Collate   |    Ctype    | ICU Locale | ICU Rules |   Access privileges   
----------------------+----------+----------+-----------------+-------------+-------------+------------+-----------+-----------------------
 arch_vacuum_overview | postgres | UTF8     | libc            | ru_RU.UTF-8 | ru_RU.UTF-8 |            |           | 
 arch_wal_overview    | postgres | UTF8     | libc            | ru_RU.UTF-8 | ru_RU.UTF-8 |            |           | 
 chekushkin           | postgres | UTF8     | libc            | ru_RU.UTF-8 | ru_RU.UTF-8 |            |           | =Tc/postgres         +
                      |          |          |                 |             |             |            |           | postgres=CTc/postgres+
                      |          |          |                 |             |             |            |           | oleg=CTc/postgres
 data_catalog         | postgres | UTF8     | libc            | ru_RU.UTF-8 | ru_RU.UTF-8 |            |           | 
 postgres             | postgres | UTF8     | libc            | ru_RU.UTF-8 | ru_RU.UTF-8 |            |           | 
 template0            | postgres | UTF8     | libc            | ru_RU.UTF-8 | ru_RU.UTF-8 |            |           | =c/postgres          +
                      |          |          |                 |             |             |            |           | postgres=CTc/postgres
 template1            | postgres | UTF8     | libc            | ru_RU.UTF-8 | ru_RU.UTF-8 |            |           | =c/postgres          +
                      |          |          |                 |             |             |            |           | postgres=CTc/postgres
 test                 | postgres | UTF8     | libc            | ru_RU.UTF-8 | ru_RU.UTF-8 |            |           | 
(8 rows)


Список баз данных можно посмотреть и в самой базе данных:
SELECT datname, datistemplate, datallowconn, datconnlimit FROM pg_database;

       datname        | datistemplate | datallowconn | datconnlimit 
----------------------+---------------+--------------+--------------
 postgres             | f             | t            |           -1
 template1            | t             | t            |           -1
 template0            | t             | f            |           -1
 chekushkin           | f             | t            |           -1
 test                 | f             | t            |           -1
 arch_vacuum_overview | f             | t            |           -1
 arch_wal_overview    | f             | t            |           -1
 data_catalog         | f             | t            |           -1
(8 rows)


Подключимся к шаблонной базе template1: 
\c template1

digest определена в пакете pgcrypto. Установим его: 
CREATE EXTENSION pgcrypto;
CREATE EXTENSION

Теперь нам доступны функции, входящие в расширение pgcrypto. Например, можно вычислить MD5-дайджест: 
SELECT digest('Hello, world!', 'md5');
               digest               
------------------------------------
 \x6cd3556deb0da54bca060b4c39479839
(1 row)

Чтобы шаблон можно было использовать для создания базы, к нему не должно быть активных подключений, поэтому отключимся от базы template1. 
\q

Для создания новой базы данных служит команда CREATE DATABASE: 
postgres=# CREATE DATABASE db;
CREATE DATABASE

\c db

Базу данных можно создать и из операционной системы утилитой createdb. 
db=# SELECT datname, datistemplate, datallowconn, datconnlimit FROM pg_database;
       datname        | datistemplate | datallowconn | datconnlimit 
----------------------+---------------+--------------+--------------
 postgres             | f             | t            |           -1
 template1            | t             | t            |           -1
 template0            | t             | f            |           -1
 chekushkin           | f             | t            |           -1
 test                 | f             | t            |           -1
 arch_vacuum_overview | f             | t            |           -1
 arch_wal_overview    | f             | t            |           -1
 data_catalog         | f             | t            |           -1
 db                   | f             | t            |           -1
(9 rows)


Созданную базу данных также можно переименовать 
postgres=# ALTER DATABASE db RENAME TO appdb;
ALTER DATABASE

postgres=# SELECT datname, datistemplate, datallowconn, datconnlimit FROM pg_database;
       datname        | datistemplate | datallowconn | datconnlimit 
----------------------+---------------+--------------+--------------
 postgres             | f             | t            |           -1
 template1            | t             | t            |           -1
 template0            | t             | f            |           -1
 chekushkin           | f             | t            |           -1
 test                 | f             | t            |           -1
 arch_vacuum_overview | f             | t            |           -1
 arch_wal_overview    | f             | t            |           -1
 data_catalog         | f             | t            |           -1
 appdb                | f             | t            |           -1
(9 rows)
др функции
ограничение количества подключений: 
postgres=# ALTER DATABASE appdb CONNECTION LIMIT 10;
ALTER DATABASE

SELECT datname, datistemplate, datallowconn, datconnlimit FROM pg_database;
       datname        | datistemplate | datallowconn | datconnlimit 
----------------------+---------------+--------------+--------------
 postgres             | f             | t            |           -1
 template1            | t             | t            |           -1
 template0            | t             | f            |           -1
 chekushkin           | f             | t            |           -1
 test                 | f             | t            |           -1
 arch_vacuum_overview | f             | t            |           -1
 arch_wal_overview    | f             | t            |           -1
 data_catalog         | f             | t            |           -1
 appdb                | f             | t            |           10
(9 rows)

Размер базы данных 
postgres=# SELECT pg_database_size('appdb');
 pg_database_size 
------------------
          7778787
(1 row)


