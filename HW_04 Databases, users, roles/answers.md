**Описание/Пошаговая инструкция выполнения домашнего задания:**
* создайте новый кластер PostgresSQL 14
  * Done
    ```
    anton@postgres2:~$ sudo apt install postgresql-14
    [sudo] password for anton:
    Чтение списков пакетов… Готово
    Построение дерева зависимостей… Готово
    Чтение информации о состоянии… Готово
    Будут установлены следующие дополнительные пакеты:
      postgresql-client-14
    Предлагаемые пакеты:
      postgresql-doc-14
    Следующие НОВЫЕ пакеты будут установлены:
      postgresql-14 postgresql-client-14
    Обновлено 0 пакетов, установлено 2 новых пакетов, для удаления отмечено 0 пакетов, и 26 пакетов не обновлено.
    Необходимо скачать 18,1 MB архивов.
    После данной операции объём занятого дискового пространства возрастёт на 59,0 MB.
    Хотите продолжить? [Д/н]
    Пол:1 https://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 postgresql-client-14 amd64 14.12-1.pgdg22.04+1 [1 621 kB]
    Пол:2 https://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 postgresql-14 amd64 14.12-1.pgdg22.04+1 [16,4 MB]
    Получено 18,1 MB за 3с (5 892 kB/s)
    Предварительная настройка пакетов …
    Выбор ранее не выбранного пакета postgresql-client-14.
    (Чтение базы данных … на данный момент установлено 76928 файлов и каталогов.)
    Подготовка к распаковке …/postgresql-client-14_14.12-1.pgdg22.04+1_amd64.deb …
    Распаковывается postgresql-client-14 (14.12-1.pgdg22.04+1) …
    Выбор ранее не выбранного пакета postgresql-14.
    Подготовка к распаковке …/postgresql-14_14.12-1.pgdg22.04+1_amd64.deb …
    Распаковывается postgresql-14 (14.12-1.pgdg22.04+1) …
    Настраивается пакет postgresql-client-14 (14.12-1.pgdg22.04+1) …
    Настраивается пакет postgresql-14 (14.12-1.pgdg22.04+1) …
    Creating new PostgreSQL cluster 14/main ...
    /usr/lib/postgresql/14/bin/initdb -D /var/lib/postgresql/14/main --auth-local peer --auth-host scram-sha-256 --no-instructions
    Файлы, относящиеся к этой СУБД, будут принадлежать пользователю "postgres".
    От его имени также будет запускаться процесс сервера.

    Кластер баз данных будет инициализирован с локалью "ru_RU.UTF-8".
    Кодировка БД по умолчанию, выбранная в соответствии с настройками: "UTF8".
    Выбрана конфигурация текстового поиска по умолчанию "russian".

    Контроль целостности страниц данных отключён.

    исправление прав для существующего каталога /var/lib/postgresql/14/main... ок
    создание подкаталогов... ок
    выбирается реализация динамической разделяемой памяти... posix
    выбирается значение max_connections по умолчанию... 100
    выбирается значение shared_buffers по умолчанию... 128MB
    выбирается часовой пояс по умолчанию... Etc/UTC
    создание конфигурационных файлов... ок
    выполняется подготовительный скрипт... ок
    выполняется заключительная инициализация... ок
    сохранение данных на диске... ок
    Обрабатываются триггеры для postgresql-common (260.pgdg22.04+1) …
    Building PostgreSQL dictionaries from installed myspell/hunspell packages...
    Removing obsolete dictionary files:
    Scanning processes...
    Scanning linux images...

    Running kernel seems to be up-to-date.

    No services need to be restarted.

    No containers need to be restarted.

    No user sessions are running outdated binaries.

    No VM guests are running outdated hypervisor (qemu) binaries on this host.
    anton@postgres2:~$ pg_lsclusters
    Ver Cluster Port Status Owner    Data directory              Log file
    14  main    5433 online postgres /var/lib/postgresql/14/main /var/log/postgresql/postgresql-14-main.log
    15  main    5432 online postgres /mnt/data/15/main           /var/log/postgresql/postgresql-15-main.log
    anton@postgres2:~$
    ```
* зайдите в созданный кластер под пользователем postgres
  * Done
    ```
    anton@postgres2:~$ sudo su - postgres -c "psql -p 5433"
    psql (15.7 (Ubuntu 15.7-1.pgdg22.04+1), сервер 14.12 (Ubuntu 14.12-1.pgdg22.04+1))
    Введите "help", чтобы получить справку.

    postgres=# select version();
                                                                   version
    -------------------------------------------------------------------------------------------------------------------------------------
     PostgreSQL 14.12 (Ubuntu 14.12-1.pgdg22.04+1) on x86_64-pc-linux-gnu, compiled by gcc (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0, 64-bit
    (1 строка)

    postgres=#
    ```
* создайте новую базу данных testdb
  * Done
    ```
    postgres=# CREATE DATABASE testdb;
    CREATE DATABASE
    postgres=#
    ```
* зайдите в созданную базу данных под пользователем postgres
  * Done
    ```
    postgres=# \c testdb
    psql (15.7 (Ubuntu 15.7-1.pgdg22.04+1), сервер 14.12 (Ubuntu 14.12-1.pgdg22.04+1))
    Вы подключены к базе данных "testdb" как пользователь "postgres".
    testdb=#
    ```
* создайте новую схему testnm
  * Done
    ```
    testdb=# CREATE SCHEMA testnm;
    CREATE SCHEMA
    testdb=#
    ```
* создайте новую таблицу t1 с одной колонкой c1 типа integer
  * Done
    ```
    testdb=# CREATE TABLE t1 (c1 INTEGER);
    CREATE TABLE
    testdb=#
    ```
* вставьте строку со значением c1=1
  * Done
    ```
    testdb=# INSERT INTO t1 (c1) VALUES (1);
    INSERT 0 1
    testdb=#
    ```
* создайте новую роль readonly
  * Done
    ```
    testdb=# CREATE ROLE readonly;
    CREATE ROLE
    testdb=#
    ```
* дайте новой роли право на подключение к базе данных testdb
  * Done
    ```
    testdb=# GRANT CONNECT ON DATABASE testdb TO readonly;
    GRANT
    testdb=#
    ```
* дайте новой роли право на использование схемы testnm
  * Done
    ```
    testdb=# GRANT USAGE ON SCHEMA testnm TO readonly;
    GRANT
    testdb=#
    ```
* дайте новой роли право на select для всех таблиц схемы testnm
  * Done
    ```
    testdb=# GRANT SELECT ON ALL TABLES IN SCHEMA testnm TO readonly;
    GRANT
    testdb=# ALTER DEFAULT PRIVILEGES FOR ROLE postgres IN SCHEMA testnm GRANT SELECT ON TABLES TO readonly;
    ALTER DEFAULT PRIVILEGES
    testdb=#
    ```
* создайте пользователя testread с паролем test123
  * Done
    ```
    testdb=# CREATE ROLE testread PASSWORD 'test123' LOGIN;
    CREATE ROLE
    testdb=#
    ```
* дайте роль readonly пользователю testread
  * Done
    ```
    testdb=# GRANT readonly TO testread;
    GRANT ROLE
    testdb=#
    ```
* зайдите под пользователем testread в базу данных testdb
  * Разрешим вход с паролем в pg_hba
    ```
    anton@postgres2:~$ sudo vim /etc/postgresql/14/main/pg_hba.conf
    anton@postgres2:~$ sudo grep -v '^#' /etc/postgresql/14/main/pg_hba.conf | grep -v '^$'
    local   all             postgres                                peer
    local   testdb          testread                                scram-sha-256
    local   all             all                                     peer
    host    all             all             127.0.0.1/32            scram-sha-256
    host    all             all             ::1/128                 scram-sha-256
    local   replication     all                                     peer
    host    replication     all             127.0.0.1/32            scram-sha-256
    host    replication     all             ::1/128                 scram-sha-256
    anton@postgres2:~$
    ```
  * Перечитаем конфигурацию в PostgreSQL
    ```
    anton@postgres2:~$ sudo systemctl reload postgresql@14-main
    ```
  * Зайдём
    ```
    anton@postgres2:~$ psql -p 5433 -U testread testdb
    Пароль пользователя testread:
    psql (15.7 (Ubuntu 15.7-1.pgdg22.04+1), сервер 14.12 (Ubuntu 14.12-1.pgdg22.04+1))
    Введите "help", чтобы получить справку.

    testdb=>
    ```
* сделайте select * from t1;
  * Fail
    ```
    testdb=> SELECT * FROM t1;
    ОШИБКА:  нет доступа к таблице t1
    testdb=>
    ```
* получилось? (могло если вы делали сами не по шпаргалке и не упустили один существенный момент про который позже)
  * Нет, делалось строго как указано
* напишите что именно произошло в тексте домашнего задания
  * В задании не указано, в какой схеме создавать таблицу. Поскольку search_path не изменялся и схемы, совпадающей с именем пользователя не существует, то таблица без явного указания схемы создалась в схеме public
    ```
    anton@postgres2:~$ sudo su - postgres -c "psql -p 5433 testdb"
    psql (15.7 (Ubuntu 15.7-1.pgdg22.04+1), сервер 14.12 (Ubuntu 14.12-1.pgdg22.04+1))
    Введите "help", чтобы получить справку.

    testdb=# SELECT n.nspname, c.relname
    testdb-# FROM   pg_namespace n
    testdb-#   JOIN pg_class c ON c.relnamespace=n.oid
    testdb-# WHERE c.relname='t1';
     nspname | relname
    ---------+---------
     public  | t1
    (1 строка)

    testdb=#
    ```
* у вас есть идеи почему? ведь права то дали?
  * Права дали на таблицу в схеме testnm, а таблица находится в public
* посмотрите на список таблиц
  * Done
    ```
    testdb=# \dt
             Список отношений
     Схема  | Имя |   Тип   | Владелец
    --------+-----+---------+----------
     public | t1  | таблица | postgres
    (1 строка)

    testdb=#
    ```
* подсказка в шпаргалке под пунктом 20
  * Эмм
* а почему так получилось с таблицей (если делали сами и без шпаргалки то может у вас все нормально)
  * См. выше
* вернитесь в базу данных testdb под пользователем postgres
  * Уже
* удалите таблицу t1
  * Done
    ```
    testdb=# DROP TABLE t1;
    DROP TABLE
    testdb=#
    ```
* создайте ее заново но уже с явным указанием имени схемы testnm
  * Done
    ```
    testdb=# CREATE TABLE testnm.t1 (c1 INT);
    CREATE TABLE
    testdb=#
    ```
* вставьте строку со значением c1=1
  * Done
    ```
    testdb=# INSERT INTO testnm.t1 (c1) VALUES (1);
    INSERT 0 1
    testdb=#
    ```
* зайдите под пользователем testread в базу данных testdb
  * Done
    ```
    testdb=#
    \q
    anton@postgres2:~$ psql -p 5433 -U testread testdb
    Пароль пользователя testread:
    psql (15.7 (Ubuntu 15.7-1.pgdg22.04+1), сервер 14.12 (Ubuntu 14.12-1.pgdg22.04+1))
    Введите "help", чтобы получить справку.

    testdb=>
    ```
* сделайте select * from testnm.t1;
  * Done
    ```
    testdb=> SELECT * FROM testnm.t1;
     c1
    ----
      1
    (1 строка)

    testdb=>
    ```
* получилось?
  * Да
* есть идеи почему? если нет - смотрите шпаргалку
  * Потому что мы изменили DEFAULT PRIVILEGES и на вновь созданную таблицу применились права, которые там указаны.
    ```
    testdb=# SELECT n.nspname, d.defaclacl, c.relname, c.relacl from pg_class c
    testdb-# JOIN             pg_namespace n on n.oid=c.relnamespace
    testdb-# LEFT OUTER JOIN  pg_default_acl d on d.defaclnamespace=n.oid
    testdb-# WHERE n.nspname not in ('pg_catalog', 'pg_toast', 'information_schema');
     nspname |       defaclacl       | relname |                     relacl
    ---------+-----------------------+---------+-------------------------------------------------
     testnm  | {readonly=r/postgres} | t1      | {postgres=arwdDxt/postgres,readonly=r/postgres}
    (1 строка)

    testdb=#
    ```
* как сделать так чтобы такое больше не повторялось? если нет идей - смотрите шпаргалку
  * Отозвать права в DEFAULT PRIVILEGES и отозвать прва на существующие таблицы для readonly
  * Либо отозвать права readonly от testread
* сделайте select * from testnm.t1;
  * Done
    ```
    testdb=> SELECT * FROM testnm.t1;
     c1
    ----
      1
    (1 строка)

    testdb=>
    ```
* получилось?
  * Да
* есть идеи почему? если нет - смотрите шпаргалку
  * Так мы с прошлого раза ничего не изменили
* сделайте select * from testnm.t1;
  * Done
    ```
    testdb=> SELECT * FROM testnm.t1;
     c1
    ----
      1
    (1 строка)

    testdb=>
    ```
* получилось?
  * Да
* ура!
  * Эмм
* теперь попробуйте выполнить команду create table t2(c1 integer); insert into t2 values (2);
  * Done
    ```
    testdb=> CREATE TABLE t2(c1 INT); INSERT INTO t2 VALUES (2);
    CREATE TABLE
    INSERT 0 1
    testdb=>
    ```
* а как так? нам же никто прав на создание таблиц и insert в них под ролью readonly?
  * Опять-таки, схема явно не указана и таблица создалась в public. Схема public является "Общедоступной"
    ```
    testdb=> SELECT n.nspname, c.relname
    testdb-> FROM   pg_namespace n
    testdb->   JOIN pg_class c ON c.relnamespace=n.oid
    testdb-> WHERE c.relkind='r'
    testdb->   AND n.nspname NOT IN ('pg_catalog', 'pg_toast', 'information_schema');
     nspname | relname
    ---------+---------
     testnm  | t1
     public  | t2
    (2 строки)

    testdb=> SELECT nspname, nspacl FROM pg_namespace;
          nspname       |                   nspacl
    --------------------+--------------------------------------------
     pg_toast           |
     pg_catalog         | {postgres=UC/postgres,=U/postgres}
     public             | {postgres=UC/postgres,=UC/postgres}
     information_schema | {postgres=UC/postgres,=U/postgres}
     testnm             | {postgres=UC/postgres,readonly=U/postgres}
    (5 строк)

    testdb=>
    ```
* есть идеи как убрать эти права? если нет - смотрите шпаргалку
  * отозвать все права на схему public от псевдороли public
    ```
    anton@postgres2:~$ sudo su - postgres -c "psql -p 5433 testdb"
    [sudo] password for anton:
    psql (15.7 (Ubuntu 15.7-1.pgdg22.04+1), сервер 14.12 (Ubuntu 14.12-1.pgdg22.04+1))
    Введите "help", чтобы получить справку.

    testdb=# REVOKE ALL ON SCHEMA public FROM public;
    REVOKE
    testdb=# SELECT nspname, nspacl FROM pg_namespace;
          nspname       |                   nspacl
    --------------------+--------------------------------------------
     pg_toast           |
     pg_catalog         | {postgres=UC/postgres,=U/postgres}
     information_schema | {postgres=UC/postgres,=U/postgres}
     testnm             | {postgres=UC/postgres,readonly=U/postgres}
     public             | {postgres=UC/postgres}
    (5 строк)

    testdb=#
    ```
* если вы справились сами то расскажите что сделали и почему, если смотрели шпаргалку - объясните что сделали и почему выполнив указанные в ней команды
  * Шпаргалкку не смотрел. Псевдороль public включает в себя все возможные (существующие и те, которые будут созданы в дальнейшем) роли. 
* теперь попробуйте выполнить команду create table t3(c1 integer); insert into t2 values (2);
  * Fail
    ```
    testdb=> CREATE TABLE t3(c1 INT); INSERT INTO t2 VALUES (2);
    ОШИБКА:  схема для создания объектов не выбрана
    СТРОКА 1: CREATE TABLE t3(c1 INT);
                           ^
    ОШИБКА:  отношение "t2" не существует
    СТРОКА 1: INSERT INTO t2 VALUES (2);
                          ^
    testdb=>
    ```
* расскажите что получилось и почему
  * В команде создания таблицы в явном виде не указана схема. Все схемы, которые указаны в search_path недоступны для пользователя testread, поэтому команда CREATE TABLE завершилась с ошибкой. 
  * Таблица t2 существует только в схеме public. Но схема public недоступна для testread, поэтому команда INSERT не смогла вставить данные и тоже завершилась с ошибкой

