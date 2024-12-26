выбор schemaname, tablename, tableowner из pg_tables с максимальным лимитом 5
postgres=# SELECT schemaname, tablename, tableowner FROM pg_tables LIMIT 5;
 schemaname |       tablename       | tableowner 
------------+-----------------------+------------
 pg_catalog | pg_statistic          | postgres
 pg_catalog | pg_type               | postgres
 pg_catalog | pg_foreign_table      | postgres
 pg_catalog | pg_authid             | postgres
 pg_catalog | pg_statistic_ext_data | postgres
(5 rows)

 выбрать все из pg_tables где название таблицы = 'pg_proc' (\gx Расширенный формат можно установить только для одного запроса, если в конце вместо точки с запятой указать \gx: )
postgres=# SELECT * FROM pg_tables WHERE tablename = 'pg_proc' \gx
-[ RECORD 1 ]-----------
schemaname  | pg_catalog
tablename   | pg_proc
tableowner  | postgres
tablespace  | 
hasindexes  | t
hasrules    | f
hastriggers | f
rowsecurity | f

postgres=# SELECT schemaname, tablename, tableowner FROM pg_tables LIMIT 5;
 schemaname |       tablename       | tableowner 
------------+-----------------------+------------
 pg_catalog | pg_statistic          | postgres
 pg_catalog | pg_type               | postgres
 pg_catalog | pg_foreign_table      | postgres
 pg_catalog | pg_authid             | postgres
 pg_catalog | pg_statistic_ext_data | postgres
(5 rows)

можно вывести результат запроса на экран, пронумеровав строки: 
postgres=# SELECT format('SELECT count(*) FROM %I;', tablename)
FROM pg_tables
LIMIT 3
\g (tuples_only=on format=unaligned) | cat -n
     1	SELECT count(*) FROM pg_statistic;
     2	SELECT count(*) FROM pg_type;
     3	SELECT count(*) FROM pg_foreign_table;

В команде \g можно указать имя файла, в который будет направлен вывод: 
ostgres=# SELECT format('SELECT count(*) FROM %I;', tablename)
FROM pg_tables
LIMIT 3
\g
 count 
-------
   409
(1 row)

 count 
-------
   613
(1 row)

 count 
-------
     0
(1 row)

postgres=# SELECT now() AS curr_time \gset
postgres=# \echo :curr_time
2024-10-28 10:36:27.079312+03

postgres=# \d pg_tables
              View "pg_catalog.pg_tables"
   Column    |  Type   | Collation | Nullable | Default 
-------------+---------+-----------+----------+---------
 schemaname  | name    |           |          | 
 tablename   | name    |           |          | 
 tableowner  | name    |           |          | 
 tablespace  | name    |           |          | 
 hasindexes  | boolean |           |          | 
 hasrules    | boolean |           |          | 
 hastriggers | boolean |           |          | 
 rowsecurity | boolean |           |          | 

запишем в переменную top5 текст запроса на получение пяти самых больших по размеру таблиц: 

postgres=# \set top5 'SELECT tablename, pg_total_relation_size(schemaname||''.''||tablename) AS bytes FROM pg_tables ORDER BY bytes DESC LIMIT 5;'
 Для выполнения запроса достаточно набрать:
:top5
postgres=# :top5
   tablename    |  bytes  
----------------+---------
 pg_proc        | 1245184
 pg_rewrite     |  745472
 pg_attribute   |  720896
 pg_description |  630784
 pg_statistic   |  294912
(5 rows)

