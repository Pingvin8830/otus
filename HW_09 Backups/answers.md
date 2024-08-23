**Описание/Пошаговая инструкция выполнения домашнего задания:**
* Создаем ВМ/докер c ПГ.
  * Done
* Создаем БД, схему и в ней таблицу.
  * Done
    ```
    anton@postgres1:~$ sudo su - postgres -c "psql -p 5433"
    psql (16.3 (Ubuntu 16.3-1.pgdg22.04+1))
    Введите "help", чтобы получить справку.

    postgres=# CREATE DATABASE backups;
    CREATE DATABASE
    postgres=# \c backups
    Вы подключены к базе данных "backups" как пользователь "postgres".
    backups=# CREATE SCHEMA schema;
    CREATE SCHEMA
    backups=# CREATE TABLE schema.raw_table (
    backups(# id INT,
    backups(# value VARCHAR(20)
    backups(# );
    CREATE TABLE
    backups=#
    ```
* Заполним таблицы автосгенерированными 100 записями.
  * Done
    ```
    backups=# INSERT INTO schema.raw_table SELECT generate_series, 'value ' || generate_series FROM generate_series(1, 100);
    INSERT 0 100
    backups=#
    ```
* Под линукс пользователем Postgres создадим каталог для бэкапов
  * Done
    ```
    backups=#
    \q
    anton@postgres1:~$ sudo su - postgres
    postgres@postgres1:~$ pwd
    /var/lib/postgresql
    postgres@postgres1:~$ mkdir -v backups
    mkdir: created directory 'backups'
    postgres@postgres1:~$
    ```
* Сделаем логический бэкап используя утилиту COPY
  * Done
    ```
    postgres@postgres1:~$ psql -p 5433 backups
    psql (16.3 (Ubuntu 16.3-1.pgdg22.04+1))
    Введите "help", чтобы получить справку.

    backups=# COPY schema.raw_table TO '/var/lib/postgresql/backups/backups__schema__raw_table.out';
    COPY 100
    backups=#
    ```
* Восстановим в 2 таблицу данные из бэкапа.
  * Done
    ```
    backups=# CREATE TABLE schema.restore_table (
    backups(# id INT,
    backups(# value VARCHAR(20)
    backups(# );
    CREATE TABLE
    backups=# COPY schema.restore_table FROM '/var/lib/postgresql/backups/backups__schema__raw_table.out';
    COPY 100
    backups=#
    ```
* Используя утилиту pg_dump создадим бэкап в кастомном сжатом формате двух таблиц
  * Done
    ```
    backups=#
    \q
    postgres@postgres1:~$ pg_dump -p 5433 -F c -f /var/lib/postgresql/backups/backups.gz backups
    postgres@postgres1:~$
    ```
* Используя утилиту pg_restore восстановим в новую БД только вторую таблицу!
  * Done
    ```
    postgres@postgres1:~$ psql -p 5433
    psql (16.3 (Ubuntu 16.3-1.pgdg22.04+1))
    Введите "help", чтобы получить справку.

    postgres=# DROP DATABASE backups;
    DROP DATABASE
    postgres=# CREATE DATABASE backups;
    CREATE DATABASE
    postgres=# \c backups
    Вы подключены к базе данных "backups" как пользователь "postgres".
    backups=# CREATE SCHEMA schema;
    CREATE SCHEMA
    backups=#
    \q
    postgres@postgres1:~$ pg_restore -p 5433 -d backups -t restore_table -c /var/lib/postgresql/backups/backups.gz
    pg_restore: ошибка: could not execute query: ОШИБКА:  таблица "restore_table" не существует
    Выполнялась команда: DROP TABLE schema.restore_table;
    pg_restore: предупреждение: при восстановлении проигнорировано ошибок: 1
    postgres@postgres1:~$ psql -p 5433
    psql (16.3 (Ubuntu 16.3-1.pgdg22.04+1))
    Введите "help", чтобы получить справку.
    
    postgres=# \c backups
    Вы подключены к базе данных "backups" как пользователь "postgres".
    backups=# SELECT * FROM schema.restore_table LIMIT 5;
     id |  value
    ----+---------
      1 | value 1
      2 | value 2
      3 | value 3
      4 | value 4
      5 | value 5
    (5 строк)

    backups=# SELECT * FROM schema.raw_table LIMIT 5;
    ОШИБКА:  отношение "schema.raw_table" не существует
    СТРОКА 1: SELECT * FROM schema.raw_table LIMIT 5;
                            ^
    backups=#
    ```

