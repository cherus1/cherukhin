-- Создать базу данных backup_overview
postgres=# CREATE DATABASE backup_overview;

-- Подключиться к базе данных backup_overview
\c backup_overview

-- Создать таблицу t с полями id (числовой) и s (текст)
backup_overview=# CREATE TABLE t(id numeric, s text);

-- Вставить данные в таблицу t
backup_overview=# INSERT INTO t VALUES (1, 'Привет!'), (2, ''), (3, NULL);

-- Выбрать все данные из таблицы t
backup_overview=# SELECT  FROM t;

-- Выгрузить данные из таблицы t в стандартный вывод
backup_overview=# COPY t TO STDOUT;

-- Очистить таблицу t
backup_overview=# TRUNCATE TABLE t;

-- Загрузить данные в таблицу t из стандартного ввода
backup_overview=# COPY t FROM STDIN;
Enter data to be copied followed by a newline. End with a backslash and
a period on a line by itself, or an EOF signal.
>> \pset null '<null>'
>> SELECT  FROM t;
```