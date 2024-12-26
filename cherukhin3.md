Создадим таблицу с одной строкой: 
postgres=# CREATE TABLE t(
  id integer,
  s text);
CREATE TABLE
postgres=# INSERT INTO t(id, s) VALUES (1, 'foo');
INSERT 0 1
postgres=# SELECT * FROM t;
увидели вывод одной строки
 id |  s  
----+-----
  1 | foo
(1 row)


postgres=# INSERT INTO t(id, s) VALUES (2, 'bar');
INSERT 0 1
postgres=# \set AUTOCOMMIT on \\\ автосохранение 
postgres=# INSERT INTO t(id, s) VALUES (3, 'baz');
INSERT 0 1
postgres=# INSERT INTO t(id, s) VALUES (4, 'qux');
INSERT 0 1
postgres=# SELECT * FROM t;
вывод информации от автосохранения
 id |  s  
----+-----
  1 | foo
  2 | bar
  3 | baz
  4 | qux
(4 rows)

 КУРСОРЫ 
 
  При выполнении команды SELECT сервер передает, а клиент получает сразу все строки:

=> SELECT * FROM t ORDER BY id;

 id |  s  
----+-----
  1 | foo
  2 | bar
  3 | baz
  4 | xyz
(4 rows)

 Курсор позволяет получать данные построчно.  
 
postgres=# BEGIN;

BEGIN
postgres=*# DECLARE c CURSOR FOR SELECT * FROM t ORDER BY id;
DECLARE CURSOR
postgres=*# FETCH c;
 id |  s  
----+-----
  1 | foo
(1 row)

postgres=*# FETCH 2 c;
 id |  s  
----+-----
  2 | bar
  3 | baz
(2 rows)

postgres=*# 

