Имя конфигурационного файла содержится в доступном для чтения параметре config_file. Имя конфигурационного файла можно указать с помощью ключа командной строки при запуске postgres
postgres=# SHOW config_file;
               config_file               
-----------------------------------------
 /etc/postgresql/16/main/postgresql.conf
(1 row)
небольшой фрагмент конфигурационного файла
postgres=# SELECT pg_read_file('/etc/postgresql/16/main/postgresql.conf', 1516, 861) \g (tuples_only=on format=unaligned)
#------------------------------------------------------------------------------
# FILE LOCATIONS
#------------------------------------------------------------------------------

# The default values of these variables are driven from the -D command-line
# option or PGDATA environment variable, represented here as ConfigDir.

data_directory = '/var/lib/postgresql/16/main'		# use data in another directory
					# (change requires restart)
hba_file = '/etc/postgresql/16/main/pg_hba.conf'	# host-based authentication file
					# (change requires restart)
ident_file = '/etc/postgresql/16/main/pg_ident.conf'	# ident configuration file
					# (change requires restart)

# If external_pid_file is not explicitly set, no extra PID file is written.
external_pid_file = '/var/run/postgresql/16-main.pid'			# write an extra PID file
					# (change requires restart)


Чтобы увидеть настройки в конфигурационных файлах, можно обратиться к представлению pg_file_settings: 
postgres=# SELECT sourceline, name, setting, applied, error FROM pg_file_settings;
 sourceline |            name            |                setting                 | applied | error 
------------+----------------------------+----------------------------------------+---------+-------
         42 | data_directory             | /var/lib/postgresql/16/main            | t       | 
         44 | hba_file                   | /etc/postgresql/16/main/pg_hba.conf    | t       | 
         46 | ident_file                 | /etc/postgresql/16/main/pg_ident.conf  | t       | 
         50 | external_pid_file          | /var/run/postgresql/16-main.pid        | t       | 
         64 | port                       | 5432                                   | t       | 
         65 | max_connections            | 100                                    | t       | 
         68 | unix_socket_directories    | /var/run/postgresql                    | t       | 
        108 | ssl                        | on                                     | t       | 
        110 | ssl_cert_file              | /etc/ssl/certs/ssl-cert-snakeoil.pem   | t       | 
        113 | ssl_key_file               | /etc/ssl/private/ssl-cert-snakeoil.key | t       | 
        130 | shared_buffers             | 128MB                                  | t       | 
        153 | dynamic_shared_memory_type | posix                                  | t       | 
        247 | max_wal_size               | 1GB                                    | t       | 
        248 | min_wal_size               | 80MB                                   | t       | 
        565 | log_line_prefix            | %m [%p] %q%u@%d                        | t       | 
        603 | log_timezone               | Europe/Moscow                          | t       | 
        607 | cluster_name               | 16/main                                | t       | 
        715 | datestyle                  | iso, dmy                               | t       | 
        717 | timezone                   | Europe/Moscow                          | t       | 
        731 | lc_messages                | ru_RU.UTF-8                            | t       | 
        733 | lc_monetary                | ru_RU.UTF-8                            | t       | 
        734 | lc_numeric                 | ru_RU.UTF-8                            | t       | 
        735 | lc_time                    | ru_RU.UTF-8                            | t       | 
        741 | default_text_search_config | pg_catalog.russian                     | t       | 
(24 rows)

Содержимое файла /etc/postgresql/16/main/conf.d/work_mem.conf: 
postgres=# SELECT name, setting, unit, boot_val, reset_val,
  source, sourcefile, sourceline, pending_restart, context
FROM pg_settings
WHERE name = 'work_mem' \gxИмя конфигурационного файла содержится в доступном для чтения параметре config_file. Имя конфигурационного файла можно указать с помощью ключа командной строки при запуске postgres. 
-[ RECORD 1 ]---+---------
name            | work_mem
setting         | 4096
unit            | kB
boot_val        | 4096
reset_val       | 4096
source          | default
sourcefile      | 
sourceline      | 
pending_restart | f
context         | user

Убедимся, что параметр work_mem получил значение из второй строки: 
рostgres=# SELECT sourcefile, sourceline, name, setting, applied
FROM pg_file_settings WHERE sourcefile LIKE '%/work_mem.conf';
 sourcefile | sourceline | name | setting | applied 
------------+------------+------+---------+---------
(0 rows)

postgres=# SELECT name, setting, unit, boot_val, reset_val,
  source, sourcefile, sourceline, pending_restart, context
FROM pg_settings
WHERE name = 'work_mem'\gx
-[ RECORD 1 ]---+---------
name            | work_mem
setting         | 4096
unit            | kB
boot_val        | 4096
reset_val       | 4096
source          | default
sourcefile      | 
sourceline      | 
pending_restart | f
context         | user


ALTER SYSTEM выполняет проверку на допустимые значения.  

postgres=# ALTER SYSTEM SET work_mem TO '16MB';
ALTER SYSTEM

 В результате выполнения команды значение 16MB записано в файл postgresql.auto.conf:
 
postgres=# SELECT pg_read_file('postgresql.auto.conf')
\g (tuples_only=on format=unaligned)
# Do not edit this file manually!
# It will be overwritten by the ALTER SYSTEM command.
work_mem = '16MB'



