проверка на обновления
$ sudo apt update
скачивание postgresql из репозитория
$ sudo apt install postgresql
После завершения установки вы можете убедиться, что служба PostgreSQL активна
$ sudo systemctl is-active postgresql
Чтобы создать новую базу данных, вы должны получить доступ к программной оболочке PostgreSQL. необходимо подключиться к системе с помощью учётной записи postgres:
$ sudo su - postgres
Подключившись, выполните команду psql:
$ psql
создание пользователя и присвоение ему пароля
# CREATE USER ruslan WITH PASSWORD '1234';
создание бд
# CREATE DATABASE db;
даем все привелегии нашему пользователю
#GRANT ALL PRIVILEGES ON DATABASE db to ruslan;
