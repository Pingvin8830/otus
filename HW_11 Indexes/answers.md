**Описание/Пошаговая инструкция выполнения домашнего задания:**
* Создать индекс к какой-либо из таблиц вашей БД
  * Done
    ```
      [anton@manager ~]$ psql -U postgres -h postgres1.otus
      psql (16.3, сервер 14.13 (Ubuntu 14.13-1.pgdg22.04+1))
      SSL-соединение (протокол: TLSv1.3, шифр: TLS_AES_256_GCM_SHA384, сжатие: выкл.)
      Введите "help", чтобы получить справку.

      [postgres1.otus] postgres@postgres=# CREATE DATABASE indexes;
      CREATE DATABASE
      [postgres1.otus] postgres@postgres=# \c indexes
      psql (16.3, сервер 14.13 (Ubuntu 14.13-1.pgdg22.04+1))
      SSL-соединение (протокол: TLSv1.3, шифр: TLS_AES_256_GCM_SHA384, сжатие: выкл.)
      Вы подключены к базе данных "indexes" как пользователь "postgres".
      [postgres1.otus] postgres@indexes=# CREATE TABLE test (
      indexes(# id INT,
      indexes(# name VARCHAR(30),
      indexes(# family VARCHAR(30),
      indexes(# city VARCHAR(30)
      indexes(# );
      CREATE TABLE
      [postgres1.otus] postgres@indexes=#
      [postgres1.otus] postgres@indexes=# EXPLAIN SELECT * FROM test WHERE id<20;
                                     QUERY PLAN
      -------------------------------------------------------------------------
       Gather  (cost=1000.00..15463.33 rows=100 width=39)
         Workers Planned: 2
         ->  Parallel Seq Scan on test  (cost=0.00..14453.33 rows=42 width=39)
               Filter: (id < 20)
      (4 строки)

      [postgres1.otus] postgres@indexes=# INSERT INTO test (id, name, family, city) SELECT generate_series, 'name ' || generate_series, 'family ' || generate_series, 'city ' || generate_series FROM generate_series(1,1000000);
      INSERT 0 1000000
      [postgres1.otus] postgres@indexes=#
    ```
* Прислать текстом результат команды explain, в которой используется данный индекс
  * Done
    ```
      [postgres1.otus] postgres@indexes=# CREATE INDEX test_id_idx ON test(id);
      CREATE INDEX
      [postgres1.otus] postgres@indexes=# EXPLAIN SELECT * FROM test WHERE id<20;
                                      QUERY PLAN
      --------------------------------------------------------------------------
       Index Scan using test_id_idx on test  (cost=0.42..8.74 rows=18 width=39)
         Index Cond: (id < 20)
      (2 строки)

      [postgres1.otus] postgres@indexes=#
    ```
* Реализовать индекс для полнотекстового поиска
  
* Реализовать индекс на часть таблицы или индекс на поле с функцией
  * Done
    ```
      [postgres1.otus] postgres@indexes=# UPDATE test SET city='Saint-Petersburg' WHERE id%2=0;
      UPDATE 500000
      [postgres1.otus] postgres@indexes=#
      [postgres1.otus] postgres@indexes=# EXPLAIN SELECT * FROM test WHERE city='Saint-Petersburg';
                                QUERY PLAN
      --------------------------------------------------------------
       Seq Scan on test  (cost=0.00..26418.00 rows=501067 width=42)
         Filter: ((city)::text = 'Saint-Petersburg'::text)
      (2 строки)

      [postgres1.otus] postgres@indexes=#
      [postgres1.otus] postgres@indexes=# CREATE INDEX test_spb_idx ON test(city) WHERE city='Saint-Petersburg';
      CREATE INDEX
      [postgres1.otus] postgres@indexes=# EXPLAIN SELECT * FROM test WHERE city='Saint-Petersburg';
                                          QUERY PLAN
      -----------------------------------------------------------------------------------
       Index Scan using test_spb_idx on test  (cost=0.42..18406.74 rows=501067 width=42)
      (1 строка)

      [postgres1.otus] postgres@indexes=#
    ```
* Создать индекс на несколько полей
  * Done
    ```
      [postgres1.otus] postgres@indexes=# EXPLAIN SELECT * FROM test WHERE name='name 10' AND family='family 10';
                                               QUERY PLAN
      ---------------------------------------------------------------------------------------------
       Gather  (cost=1000.00..21168.10 rows=1 width=42)
         Workers Planned: 2
         ->  Parallel Seq Scan on test  (cost=0.00..20168.00 rows=1 width=42)
               Filter: (((name)::text = 'name 10'::text) AND ((family)::text = 'family 10'::text))
      (4 строки)

      [postgres1.otus] postgres@indexes=# CREATE INDEX test_fio_idx ON TEST (name, family);
      CREATE INDEX
      [postgres1.otus] postgres@indexes=# EXPLAIN SELECT * FROM test WHERE name='name 10' AND family='family 10';
                                              QUERY PLAN
      -------------------------------------------------------------------------------------------
       Index Scan using test_fio_idx on test  (cost=0.42..8.45 rows=1 width=42)
         Index Cond: (((name)::text = 'name 10'::text) AND ((family)::text = 'family 10'::text))
      (2 строки)

      [postgres1.otus] postgres@indexes=#
    ```
Написать комментарии к каждому из индексов
Описать что и как делали и с какими проблемами
столкнулись

Критерии оценки:
Критерии оценивания:
Выполнение ДЗ: 10 баллов
плюс 2 балла за красивое решение
минус 2 балла за рабочее решение, и недостатки указанные преподавателем не устранены
