sudo -u postgres psql
[sudo] пароль:
```
psql (16.4 (Ubuntu 16.4-0ubuntu0.24.04.2))
Type "help" for help.

postgres=# \c db1
You are now connected to database "db1" as user "postgres".
```
postgres=# SELECT  FROM t;
ERROR:  relation "t" does not exist
LINE 1: SELECT  FROM t;
                      ^
```

Создаем таблицу `t` с тремя столбцами: `id` (целое число, первичный ключ), `s` (текст).

```
postgres=# CREATE TABLE t(
    id integer GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    s text
);
CREATE TABLE
```

Вставляем в таблицу три строки: `('Привет, мир!')`, `('')` и `(NULL)`.

```
postgres=# INSERT INTO t(s) VALUES ('Привет, мир!'), (''), (NULL);
INSERT 0 3
```

Выводим содержимое таблицы с помощью команды `SELECT`.

```
postgres=# SELECT  FROM t;
 id |      s       
----+--------------
  1 | Привет, мир!
  2 | 
  3 | 
(3 rows)
```

Выгружаем содержимое таблицы в текстовый файл с помощью команды `COPY`.

```
postgres=# COPY t TO stdout;
1 Привет, мир!
2 
3 \N
```

Выгружаем содержимое таблицы в текстовый файл с помощью команды `COPY`, установив разделитель столбцов и значение для `NULL`.

```
postgres=# COPY t TO stdout WITH (NULL '<NULL>', DELIMITER ',');
1,Привет\, мир!
2,
3,<NULL>
```

Выгружаем содержимое таблицы в текстовый файл с помощью команды `COPY`, выбрав только строки, где значение столбца `s` не равно `NULL`.

```
postgres=# COPY (SELECT  FROM t WHERE s IS NOT NULL) TO stdout;
1 Привет, мир!
2 
```

Выгружаем содержимое таблицы в текстовый файл в формате CSV.

```
postgres=# COPY t TO stdout WITH (FORMAT csv);
1,"Привет, мир!"
2,""
3,
```

Очищаем таблицу `t`.

```
postgres=# TRUNCATE TABLE t;
TRUNCATE TABLE
```

Загружаем данные в таблицу `t` из стандартного ввода.

```
postgres=# COPY t FROM stdin WHERE id != 2;
Enter data to be copied followed by a newline.
End with a backslash and a period on a line by itself, or an EOF signal.
>> 1 Привет, мир!
2 
3 \N
\.>> >> >> 
COPY 2
```

Устанавливаем значение для отображения `NULL` в результатах запросов.

```
postgres=# \pset null '\\N'
Null display is "\N".
```

Выводим содержимое таблицы `t`.

```
postgres=# SELECT  FROM t;
 id |      s       
----+--------------
  1 | Привет, мир!
  3 | \N
(2 rows)
```

Очищаем таблицу `t`.

```
postgres=# TRUNCATE TABLE t;
TRUNCATE TABLE
```

Загружаем данные в таблицу `t` из стандартного ввода.

```
postgres=# COPY t FROM stdin;
Enter data to be copied followed by a newline.
End with a backslash and a period on a line by itself, or an EOF signal.
>> 1 Привет, мир!
2 
3 \N
\.>> >> >> 
COPY 3
```

Выводим содержимое таблицы `t`.

```
postgres=# SELECT  FROM t;
 id |      s       
----+--------------
  1 | Привет, мир!
  2 | 
  3 | \N
(3 rows)