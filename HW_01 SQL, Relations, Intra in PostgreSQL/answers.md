**Описание/Пошаговая инструкция выполнения домашнего задания:**
* создать новый проект в Google Cloud Platform, Яндекс облако или на любых ВМ, докере
  * Done

* далее создать инстанс виртуальной машины с дефолтными параметрами
  * Done

* добавить свой ssh ключ в metadata ВМ
  * None

* зайти удаленным ssh (первая сессия), не забывайте про ssh-add
  * None

* поставить PostgreSQL
  * Done

* зайти вторым ssh (вторая сессия)
  * Done

* запустить везде psql из под пользователя postgres
  * Done
```
      [anton@ManageServer ~]$ sudo su - postgres -c psql                                                                    │[anton@ManageServer ~]$ sudo su - postgres -c psql
      [sudo] пароль для anton:                                                                                              │[sudo] пароль для anton:
      psql (16.2)                                                                                                           │psql (16.2)
      Введите "help", чтобы получить справку.                                                                               │Введите "help", чтобы получить справку.
                                                                                                                            │
      postgres=# \pset pager off                                                                                            │postgres=# \pset pager off
      Постраничник выключен.                                                                                                │Постраничник выключен.
```
* выключить auto commit
  * Done
        postgres=# \set AUTOCOMMIT off                                                                                        │postgres=# \set AUTOCOMMIT off
        postgres=#                                                                                                            │postgres=#
    
* сделать
  в первой сессии новую таблицу и наполнить ее данными
  create table persons(id serial, first_name text, second_name text);
  insert into persons(first_name, second_name) values('ivan', 'ivanov');
  insert into persons(first_name, second_name) values('petr', 'petrov');
  commit;
  * Done
        postgres=# CREATE TABLE persons (                                                                                     │postgres=#
        postgres(#   id SERIAL,                                                                                               │postgres=#
        postgres(#   first_name TEXT,                                                                                         │postgres=#
        postgres(#   second_name TEXT                                                                                         │postgres=#
        postgres(# );                                                                                                         │postgres=#
        CREATE TABLE                                                                                                          │postgres=#
        postgres=*# INSERT INTO persons (first_name, second_name) VALUES                                                      │postgres=#
        postgres-*# ('ivan', 'ivanov');                                                                                       │postgres=#
        INSERT 0 1                                                                                                            │postgres=#
        postgres=*# INSERT INTO persons (first_name, second_name) VALUES                                                      │postgres=#
        postgres-*# ('petr', 'petrov');                                                                                       │postgres=#
        INSERT 0 1                                                                                                            │postgres=#
        postgres=*# COMMIT;                                                                                                   │postgres=#
        COMMIT                                                                                                                │postgres=#
        postgres=#                                                                                                            │postgres=#

* посмотреть текущий уровень изоляции: show transaction isolation level
  * Done
        postgres=# SHOW TRANSACTION ISOLATION LEVEL;                                                                          │postgres=# SHOW TRANSACTION ISOLATION LEVEL;
         transaction_isolation                                                                                                │ transaction_isolation
        -----------------------                                                                                               │-----------------------
         read committed                                                                                                       │ read committed
        (1 строка)                                                                                                            │(1 строка)
                                                                                                                              │
        postgres=*#                                                                                                           │postgres=*#

* начать новую транзакцию в обоих сессиях с дефолтным (не меняя) уровнем изоляции
  * Мы уже начали транзакции

* в первой сессии добавить новую запись insert into persons(first_name, second_name) values('sergey', 'sergeev');
  * Done
        postgres=*# INSERT INTO persons (first_name, second_name) VALUES                                                      │postgres=*#
        postgres-*# ('sergey', 'sergeev');                                                                                    │postgres=*#
        INSERT 0 1                                                                                                            │postgres=*#
        postgres=*#                                                                                                           │postgres=*#

* сделать select from persons во второй сессии
  * Done
        postgres=*#                                                                                                           │postgres=*# SELECT * FROM persons;
        postgres=*#                                                                                                           │ id | first_name | second_name
        postgres=*#                                                                                                           │----+------------+-------------
        postgres=*#                                                                                                           │  1 | ivan       | ivanov
        postgres=*#                                                                                                           │  2 | petr       | petrov
        postgres=*#                                                                                                           │(2 строки)
        postgres=*#                                                                                                           │
        postgres=*#                                                                                                           │postgres=*#

* видите ли вы новую запись и если да то почему?
 * Нет. Потому что первая транзакция не завершена. Грязное чтение не допускается в PostgreSQL

* завершить первую транзакцию - commit;
  * Done
        postgres=*# COMMIT;                                                                                                   │postgres=*#
        COMMIT                                                                                                                │postgres=*#
        postgres=#                                                                                                            │postgres=*#

* сделать select from persons во второй сессии
  * Done
        postgres=#                                                                                                            │ id | first_name | second_name
        postgres=#                                                                                                            │----+------------+-------------
        postgres=#                                                                                                            │  1 | ivan       | ivanov
        postgres=#                                                                                                            │  2 | petr       | petrov
        postgres=#                                                                                                            │  3 | sergey     | sergeev
        postgres=#                                                                                                            │(3 строки)
        postgres=#                                                                                                            │
        postgres=#                                                                                                            │postgres=*#

* видите ли вы новую запись и если да то почему?
  * Да. Потому что первая транзакция успешно завершена, данные доступны другим транзакциям с уровнем изоляции READ COMMITED

* завершите транзакцию во второй сессии
  * Done
        postgres=#                                                                                                            │postgres=*# COMMIT;
        postgres=#                                                                                                            │COMMIT
        postgres=#                                                                                                            │postgres=#

* начать новые но уже repeatable read транзации - set transaction isolation level repeatable read;
  * Done
        postgres=# SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;                                                           │postgres=# SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;
        SET                                                                                                                   │SET
        postgres=*# SHOW TRANSACTION ISOLATION LEVEL;                                                                         │postgres=*# SHOW TRANSACTION ISOLATION LEVEL;
         transaction_isolation                                                                                                │ transaction_isolation
        -----------------------                                                                                               │-----------------------
         repeatable read                                                                                                      │ repeatable read
        (1 строка)                                                                                                            │(1 строка)
                                                                                                                              │
        postgres=*#                                                                                                           │postgres=*#

* в первой сессии добавить новую запись insert into persons(first_name, second_name) values('sveta', 'svetova');
  * Done
        postgres=*# INSERT INTO persons (first_name, second_name) VALUES                                                      │postgres=*#
        postgres-*# ('sveta', 'svetova');                                                                                     │postgres=*#
        INSERT 0 1                                                                                                            │postgres=*#
        postgres=*#                                                                                                           │postgres=*#

* сделать select* from persons во второй сессии*
  * Done
        postgres=*#                                                                                                           │postgres=*# SELECT * FROM persons;
        postgres=*#                                                                                                           │ id | first_name | second_name
        postgres=*#                                                                                                           │----+------------+-------------
        postgres=*#                                                                                                           │  1 | ivan       | ivanov
        postgres=*#                                                                                                           │  2 | petr       | petrov
        postgres=*#                                                                                                           │  3 | sergey     | sergeev
        postgres=*#                                                                                                           │(3 строки)
        postgres=*#                                                                                                           │
        postgres=*#                                                                                                           │postgres=*#

* видите ли вы новую запись и если да то почему?
  * Нет, потому что первая транзакия не завершена. Грязное чтение не допускается

* завершить первую транзакцию - commit;
  * Done
        postgres=*# COMMIT;                                                                                                   │postgres=*#
        COMMIT                                                                                                                │postgres=*#
        postgres=#                                                                                                            │postgres=*#

* сделать select from persons во второй сессии
  * Done
        postgres=#                                                                                                            │postgres=*# SELECT * FROM persons;
        postgres=#                                                                                                            │ id | first_name | second_name
        postgres=#                                                                                                            │----+------------+-------------
        postgres=#                                                                                                            │  1 | ivan       | ivanov
        postgres=#                                                                                                            │  2 | petr       | petrov
        postgres=#                                                                                                            │  3 | sergey     | sergeev
        postgres=#                                                                                                            │(3 строки)
        postgres=#                                                                                                            │
        postgres=#                                                                                                            │postgres=*#

* видите ли вы новую запись и если да то почему?
  * Нет. Потому что набор данных изменился после начала второй транзакции. Уровень изоляции REPEATABLE READ не допускает неповторяющееся чтение

* завершить вторую транзакцию
  * Done
        postgres=#                                                                                                            │postgres=*# COMMIT;
        postgres=#                                                                                                            │COMMIT
        postgres=#                                                                                                            │postgres=#

* сделать select * from persons во второй сессии
  * Done
        postgres=#                                                                                                            │postgres=# SELECT * FROM persons;
        postgres=#                                                                                                            │ id | first_name | second_name
        postgres=#                                                                                                            │----+------------+-------------
        postgres=#                                                                                                            │  1 | ivan       | ivanov
        postgres=#                                                                                                            │  2 | petr       | petrov
        postgres=#                                                                                                            │  3 | sergey     | sergeev
        postgres=#                                                                                                            │  4 | sveta      | svetova
        postgres=#                                                                                                            │(4 строки)
        postgres=#                                                                                                            │
        postgres=#                                                                                                            │postgres=*#

* видите ли вы новую запись и если да то почему? ДЗ сдаем в виде миниотчета в markdown в гите
  * Да. Во второй сессии предыдущая транзакция завершена, началась новая транзакция. Добавленные в первой сессии данные были вставлены ранее новой транзакции, они доступны ей для работы.

