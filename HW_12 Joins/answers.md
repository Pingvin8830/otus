**Описание/Пошаговая инструкция выполнения домашнего задания:**
* Реализовать прямое соединение двух или более таблиц
  * Done
    ```
      [postgres3.otus] postgres@indexes=# CREATE DATABASE joins;
      CREATE DATABASE
      [postgres3.otus] postgres@indexes=# \c joins
      psql (16.3, сервер 16.4 (Ubuntu 16.4-1.pgdg22.04+2))
      SSL-соединение (протокол: TLSv1.3, шифр: TLS_AES_256_GCM_SHA384, сжатие: выкл.)
      Вы подключены к базе данных "joins" как пользователь "postgres".
      [postgres3.otus] postgres@joins=# CREATE TABLE humans (
      joins(# id INT,
      joins(# name VARCHAR(10)
      joins(# );
      CREATE TABLE
      [postgres3.otus] postgres@joins=# CREATE TABLE animals (
      joins(# id INT,
      joins(# type INT,
      joins(# name VARCHAR(10)
      joins(# );
      CREATE TABLE
      [postgres3.otus] postgres@joins=# CREATE TABLE animal_types (
      joins(# id INT,
      joins(# name VARCHAR(10)
      joins(# );
      CREATE TABLE
      [postgres3.otus] postgres@joins=# CREATE TABLE human_animal (
      joins(# id INT,
      joins(# human INT,
      joins(# animal INT
      joins(# );
      CREATE TABLE
      [postgres3.otus] postgres@joins=#
      [postgres3.otus] postgres@joins=# INSERT INTO humans VALUES
      joins-# (1, 'Anton'),
      joins-# (2, 'Vasily'),
      joins-# (3, 'Maria'),
      joins-# (4, 'Fedor'),
      joins-# (5, 'Maksim');
      INSERT 0 5
      [postgres3.otus] postgres@joins=# INSERT INTO animal_types VALUES
      joins-# (1, 'Dog'),
      joins-# (2, 'Cat'),
      joins-# (3, 'Rabbit');
      INSERT 0 3
      [postgres3.otus] postgres@joins=# INSERT INTO animals VALUES
      joins-# (1, 1, 'Bagira'),
      joins-# (2, 1, 'Matilda'),
      joins-# (3, 2, 'Caesar'),
      joins-# (4, 2, 'Richard');
      INSERT 0 4
      [postgres3.otus] postgres@joins=# INSERT INTO human_animal VALUES
      joins-# (1, 1, 1),
      joins-# (2, 2, 2),
      joins-# (3, 5, 4);
      INSERT 0 3
      [postgres3.otus] postgres@joins=#
      [postgres3.otus] postgres@joins=# SELECT h.name human, a.name animal
      joins-# FROM humans h JOIN human_animal ha ON h.id=ha.human
      joins-# JOIN animals a ON ha.animal=a.id
      joins-# ;
        name  |  name
      --------+---------
       Anton  | Bagira
       Vasily | Matilda
       Maksim | Richard
      (3 строки)

      [postgres3.otus] postgres@joins=#
    ```
* Реализовать левостороннее (или правостороннее) соединение двух или более таблиц
  * Done
    ```
      [postgres3.otus] postgres@joins=# SELECT h.name human, a.name animal
      FROM humans h LEFT JOIN human_animal ha ON h.id=ha.human
      LEFT JOIN animals a ON ha.animal=a.id
      ;
       human  | animal
      --------+---------
       Anton  | Bagira
       Vasily | Matilda
       Maria  |
       Fedor  |
       Maksim | Richard
      (5 строк)
      
      [postgres3.otus] postgres@joins=#
    ```
* Реализовать кросс соединение двух или более таблиц
  * Done
    ```
      [postgres3.otus] postgres@joins=# SELECT h.name human, a.name animal
      FROM humans h CROSS JOIN human_animal ha
      CROSS JOIN animals a
      LIMIT 20;
       human  | animal
      --------+---------
       Anton  | Bagira
       Anton  | Bagira
       Anton  | Bagira
       Anton  | Matilda
       Anton  | Matilda
       Anton  | Matilda
       Anton  | Caesar
       Anton  | Caesar
       Anton  | Caesar
       Anton  | Richard
       Anton  | Richard
       Anton  | Richard
       Vasily | Bagira
       Vasily | Bagira
       Vasily | Bagira
       Vasily | Matilda
       Vasily | Matilda
       Vasily | Matilda
       Vasily | Caesar
       Vasily | Caesar
      (20 строк)

      [postgres3.otus] postgres@joins=#
    ```
* Реализовать полное соединение двух или более таблиц
  * Done
    ```
      [postgres3.otus] postgres@joins=# SELECT h.name human, a.name animal
      FROM humans h FULL JOIN human_animal ha ON h.id=ha.human
      FULL JOIN animals a ON a.id=ha.animal
      ;
       human  | animal
      --------+---------
       Anton  | Bagira
       Vasily | Matilda
              | Caesar
       Maksim | Richard
       Maria  |
       Fedor  |
      (6 строк)

      [postgres3.otus] postgres@joins=#
    ```
* Реализовать запрос, в котором будут использованы разные типы соединений
  * Done
    ```
      [postgres3.otus] postgres@joins=# SELECT h.name human, at.name || ' ' || a.name animal
      joins-# FROM humans h
      joins-# LEFT JOIN human_animal ha ON h.id=ha.human
      joins-# JOIN animals a ON a.id=ha.animal
      joins-# RIGHT JOIN animal_types at ON at.id=a.type
      joins-# ;
       human  |   animal
      --------+-------------
       Anton  | Dog Bagira
       Vasily | Dog Matilda
       Maksim | Cat Richard
              |
      (4 строки)

      [postgres3.otus] postgres@joins=#
      [postgres3.otus] postgres@joins=# SELECT h.name human, at.name || ' ' || a.name animal, at.name type
      FROM humans h
      LEFT JOIN human_animal ha ON h.id=ha.human
      JOIN animals a ON a.id=ha.animal
      RIGHT JOIN animal_types at ON at.id=a.type
      ;
       human  |   animal    |  type
      --------+-------------+--------
       Anton  | Dog Bagira  | Dog
       Vasily | Dog Matilda | Dog
       Maksim | Cat Richard | Cat
              |             | Rabbit
      (4 строки)

      [postgres3.otus] postgres@joins=#
    ```

