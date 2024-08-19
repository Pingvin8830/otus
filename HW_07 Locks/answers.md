**Описание/Пошаговая инструкция выполнения домашнего задания:**
* Настройте сервер так, чтобы в журнал сообщений сбрасывалась информация о блокировках, удерживаемых более 200 миллисекунд. Воспроизведите ситуацию, при которой в журнале появятся такие сообщения.
  * Done
    ```
    anton@postgres1:~$ sudo pg_createcluster 16 locks
    [sudo] password for anton:
    Creating new PostgreSQL cluster 16/locks ...
    /usr/lib/postgresql/16/bin/initdb -D /var/lib/postgresql/16/locks --auth-local peer --auth-host scram-sha-256 --no-instructions
    Файлы, относящиеся к этой СУБД, будут принадлежать пользователю "postgres".
    От его имени также будет запускаться процесс сервера.

    Кластер баз данных будет инициализирован с локалью "ru_RU.UTF-8".
    Кодировка БД по умолчанию, выбранная в соответствии с настройками: "UTF8".
    Выбрана конфигурация текстового поиска по умолчанию "russian".

    Контроль целостности страниц данных отключён.

    исправление прав для существующего каталога /var/lib/postgresql/16/locks... ок
    создание подкаталогов... ок
    выбирается реализация динамической разделяемой памяти... posix
    выбирается значение max_connections по умолчанию... 100
    выбирается значение shared_buffers по умолчанию... 128MB
    выбирается часовой пояс по умолчанию... Etc/UTC
    создание конфигурационных файлов... ок
    выполняется подготовительный скрипт... ок
    выполняется заключительная инициализация... ок
    сохранение данных на диске... ок
    Ver Cluster Port Status Owner    Data directory               Log file
    16  locks   5435 down   postgres /var/lib/postgresql/16/locks /var/log/postgresql/postgresql-16-locks.log
    anton@postgres1:~$ sudo pg_ctlcluster 16 locks start
    anton@postgres1:~$ pg_lsclusters
    Ver Cluster    Port Status Owner    Data directory                    Log file
    16  checksums  5434 online postgres /var/lib/postgresql/16/checksums  /var/log/postgresql/postgresql-16-checksums.log
    16  locks      5435 online postgres /var/lib/postgresql/16/locks      /var/log/postgresql/postgresql-16-locks.log
    16  main       5432 online postgres /var/lib/postgresql/16/main       /var/log/postgresql/postgresql-16-main.log
    16  otus_hw_06 5433 online postgres /var/lib/postgresql/16/otus_hw_06 /var/log/postgresql/postgresql-16-otus_hw_06.log
    anton@postgres1:~$
    anton@postgres1:~$ sudo su -
    root@postgres1:~# echo 'log_min_duration_statement = 200' > /etc/postgresql/16/locks/conf.d/locks.conf
    root@postgres1:~# systemctl reload postgresql@16-locks
    root@postgres1:~# su - postgres -c "psql -p 5435 -c 'SHOW log_min_duration_statement'"
     log_min_duration_statement
    ----------------------------
     200ms
    (1 строка)

    root@postgres1:~# su - postgres -c "psql -p 5435 -c 'CREATE TABLE locks (dt TIMESTAMP)'"
    CREATE TABLE
    root@postgres1:~# su - postgres -c "psql -p 5435 -c 'INSERT INTO locks SELECT now()'"
    INSERT 0 1
    root@postgres1:~# su - postgres -c "psql -p 5435 -c 'SELECT dt, pg_sleep(1) FROM locks'"
                dt             | pg_sleep
    ---------------------------+----------
     2024-08-08 18:04:15.57199 |
    (1 строка)

    root@postgres1:~# tail /var/log/postgresql/postgresql-16-locks.log
    2024-08-08 17:19:43.702 UTC [104485] СООБЩЕНИЕ:  для приёма подключений открыт Unix-сокет "/var/run/postgresql/.s.PGSQL.5435"
    2024-08-08 17:19:43.704 UTC [104488] СООБЩЕНИЕ:  система БД была выключена: 2024-08-08 17:19:32 UTC
    2024-08-08 17:19:43.706 UTC [104485] СООБЩЕНИЕ:  система БД готова принимать подключения
    2024-08-08 17:24:43.851 UTC [104486] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-08-08 17:24:48.673 UTC [104486] СООБЩЕНИЕ:  контрольная точка завершена: записано буферов: 44 (0.3%); добавлено файлов WAL 0, удалено: 0, переработано: 0; запись=4.816 сек., синхр.=0.004 сек., всего=4.823 сек.; синхронизировано_файл
     в=11, самая_долгая_синхр.=0.002 сек., средняя=0.001 сек.; расстояние=260 kB, ожидалось=260 kB; lsn=0/15283D0, lsn redo=0/1528398
    2024-08-08 17:35:10.394 UTC [104485] СООБЩЕНИЕ:  получен SIGHUP, файлы конфигурации перезагружаются
    2024-08-08 17:35:10.394 UTC [104485] СООБЩЕНИЕ:  параметр "log_min_duration_statement" принял значение "200"
    2024-08-08 18:04:34.081 UTC [104968] postgres@postgres СООБЩЕНИЕ:  продолжительность: 1051.258 мс, оператор: SELECT dt, pg_sleep(1) FROM locks
    2024-08-08 18:04:43.966 UTC [104486] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-08-08 18:04:46.647 UTC [104486] СООБЩЕНИЕ:  контрольная точка завершена: записано буферов: 24 (0.1%); добавлено файлов WAL 0, удалено: 0, переработано: 0; запись=2.672 сек., синхр.=0.005 сек., всего=2.682 сек.; синхронизировано_файл
     в=20, самая_долгая_синхр.=0.002 сек., средняя=0.001 сек.; расстояние=96 kB, ожидалось=244 kB; lsn=0/1540460, lsn redo=0/1540428
    root@postgres1:~#
    ```
* Смоделируйте ситуацию обновления одной и той же строки тремя командами UPDATE в разных сеансах. Изучите возникшие блокировки в представлении pg_locks и убедитесь, что все они понятны. Пришлите список блокировок и объясните, что значит каждая.
  * Done
    ```
    anton@postgres1:~$ sudo su - postgres -c 'psql -p 5435'                              │anton@postgres1:~$ sudo su - postgres -c 'psql -p 5435'│anton@postgres1:~$ sudo su - postgres -c 'psql -p 5435'│anton@postgres1:~$ sudo su - postgres -c 'psql -p 5435'
    [sudo] password for anton:                                                           │[sudo] password for anton:                             │[sudo] password for anton:                             │[sudo] password for anton:
    psql (16.3 (Ubuntu 16.3-1.pgdg22.04+1))                                              │psql (16.3 (Ubuntu 16.3-1.pgdg22.04+1))                │psql (16.3 (Ubuntu 16.3-1.pgdg22.04+1))                │psql (16.3 (Ubuntu 16.3-1.pgdg22.04+1))
    Введите "help", чтобы получить справку.                                              │Введите "help", чтобы получить справку.                │Введите "help", чтобы получить справку.                │Введите "help", чтобы получить справку.
                                                                                         │                                                       │                                                       │
    postgres=# \pset pager off                                                           │postgres=# SELECT pg_backend_pid();                    │postgres=# SELECT pg_backend_pid();                    │postgres=# SELECT pg_backend_pid();
    Постраничник выключен.                                                               │ pg_backend_pid                                        │ pg_backend_pid                                        │ pg_backend_pid
    postgres=# SELECT pg_backend_pid();                                                  │----------------                                       │----------------                                       │----------------
     pg_backend_pid                                                                      │         106499                                        │         106566                                        │         106573
    ----------------                                                                     │(1 строка)                                             │(1 строка)                                             │(1 строка)
             106782                                                                      │                                                       │                                                       │
    (1 строка)                                                                           │postgres=# BEGIN;                                      │postgres=# BEGIN;                                      │postgres=# BEGIN;
                                                                                         │BEGIN                                                  │BEGIN                                                  │BEGIN
    postgres=# SELECT                                                                    │postgres=*# UPDATE locks SET dt=now();                 │postgres=*# UPDATE locks SET dt=now();                 │postgres=*# UPDATE locks SET dt=now();
    postgres-#   locktype,                                                               │UPDATE 1                                               │                                                       │
    postgres-#   relation::regclass,                                                     │postgres=*#                                            │                                                       │
    postgres-#   virtualxid,                                                             │                                                       │                                                       │
    postgres-#   transactionid,                                                          │                                                       │                                                       │
    postgres-#   mode,                                                                   │                                                       │                                                       │
    postgres-#   granted                                                                 │                                                       │                                                       │
    postgres-# FROM pg_locks                                                             │                                                       │                                                       │
    postgres-# ORDER BY locktype;                                                        │                                                       │                                                       │
       locktype    | relation | virtualxid | transactionid |       mode       | granted  │                                                       │                                                       │
    ---------------+----------+------------+---------------+------------------+--------- │                                                       │                                                       │
     relation      | pg_locks |            |               | AccessShareLock  | t        │                                                       │                                                       │
     relation      | locks    |            |               | RowExclusiveLock | t        │                                                       │                                                       │
     relation      | locks    |            |               | RowExclusiveLock | t        │                                                       │                                                       │
     relation      | locks    |            |               | RowExclusiveLock | t        │                                                       │                                                       │
     transactionid |          |            |           743 | ExclusiveLock    | t        │                                                       │                                                       │
     transactionid |          |            |           744 | ExclusiveLock    | t        │                                                       │                                                       │
     transactionid |          |            |           745 | ExclusiveLock    | t        │                                                       │                                                       │
     transactionid |          |            |           743 | ShareLock        | f        │                                                       │                                                       │
     tuple         | locks    |            |               | ExclusiveLock    | t        │                                                       │                                                       │
     tuple         | locks    |            |               | ExclusiveLock    | f        │                                                       │                                                       │
     virtualxid    |          | 6/71       |               | ExclusiveLock    | t        │                                                       │                                                       │
     virtualxid    |          | 4/21       |               | ExclusiveLock    | t        │                                                       │                                                       │
     virtualxid    |          | 3/220      |               | ExclusiveLock    | t        │                                                       │                                                       │
     virtualxid    |          | 5/19       |               | ExclusiveLock    | t        │                                                       │                                                       │
    (14 строк)                                                                           │                                                       │                                                       │
                                                                                         │                                                       │                                                       │
    postgres=#                                                                           │                                                       │                                                       │
    ```
    Блокировка pg_locks (AccessShareLock) - текущий SELECT в первой сессии. Самая "мягкая" блокировка

    3 блокировки таблицы locks (RowExclusiveLock) - три UPDATE в разных сессиях держат таблицу для вставки новой версии строки

    4 блокировки transactionid (ExclusiveLock) - четыре транзакции блокируют свои ID

    2 блокировки tuple (ExclusiveLock) - два UPDATE в сессиях 3 и 4 блокируют текущую версию строки, потому что записать новую версию до завершения транзакции в сессии 2 не могут. При этом одна из блокировок уже установлена, а вторая - нет, т.к. одновременно 2 эксклюзивные блокировки одной и той же строки несовместимы

    4 блокировки virtualxid (ExclusiveLock) - 4 транзакции заблокировали свои виртуальные ID в момент старта

* Воспроизведите взаимоблокировку трех транзакций. Можно ли разобраться в ситуации постфактум, изучая журнал сообщений?
  * Done
    ```
    postgres=# CREATE TABLE accounts (acc_no INT, amount DECIMA│postgres=#                                                │postgres=#                                                 │
    L);                                                        │postgres=#                                                │postgres=#                                                 │
    CREATE TABLE                                               │postgres=#                                                │postgres=#                                                 │
    postgres=# INSERT INTO accounts VALUES                     │postgres=#                                                │postgres=#                                                 │
    postgres-# (1, 100.00),                                    │postgres=#                                                │postgres=#                                                 │
    postgres-# (2, 200.00),                                    │postgres=#                                                │postgres=#                                                 │
    postgres-# (3, 300.00);                                    │postgres=#                                                │postgres=#                                                 │
    INSERT 0 3                                                 │postgres=#                                                │postgres=#                                                 │
    postgres=# BEGIN;                                          │postgres=# BEGIN;                                         │postgres=# BEGIN;                                          │
    BEGIN                                                      │BEGIN                                                     │BEGIN                                                      │
    postgres=*# UPDATE accounts SET amount = amount-10.00 WHERE│postgres=*# UPDATE accounts SET amount=amount-20 WHERE acc│postgres=*# UPDATE accounts SET amount=amount-30 WHERE acc_│
     acc_no=1;                                                 │_no=2;                                                    │no=3;                                                      │
    UPDATE 1                                                   │UPDATE 1                                                  │UPDATE 1                                                   │
    postgres=*# UPDATE accounts SET amount=amount+10 WHERE acc_│postgres=*# UPDATE accounts SET amount=amount+20 WHERE acc│postgres=*# UPDATE accounts SET amount=amount+30 WHERE acc_│
    no=2;                                                      │_no=3;                                                    │no=1;                                                      │
                                                               │UPDATE 1                                                  │ОШИБКА:  обнаружена взаимоблокировка                       │
                                                               │postgres=*#                                               │ПОДРОБНОСТИ:  Процесс 2322 ожидает в режиме ShareLock блоки│
                                                               │                                                          │ровку "транзакция 751"; заблокирован процессом 2172.       │
                                                               │                                                          │Процесс 2172 ожидает в режиме ShareLock блокировку "транзак│
                                                               │                                                          │ция 752"; заблокирован процессом 2257.                     │
                                                               │                                                          │Процесс 2257 ожидает в режиме ShareLock блокировку "транзак│
                                                               │                                                          │ция 753"; заблокирован процессом 2322.                     │
                                                               │                                                          │ПОДСКАЗКА:  Подробности запроса смотрите в протоколе сервер│
                                                               │                                                          │а.                                                         │
                                                               │                                                          │КОНТЕКСТ:  при изменении кортежа (0,1) в отношении "account│
                                                               │                                                          │s"                                                         │
                                                               │                                                          │postgres=!#                                                │

    anton@postgres1:~$ sudo cat /var/log/postgresql/postgresql-16-locks.log
    2024-08-19 11:56:15.076 UTC [872] СООБЩЕНИЕ:  контрольная точка завершена: записано буферов: 24 (0.1%); добавлено файлов WAL 0, удалено: 0, переработано: 0; запись=2.309 сек., синхр.=0.005 сек., всего=2.318 сек.; синхронизировано_файлов=20, самая_долгая_синхр.=0.002 сек., средняя=0.001 сек.; расстояние=130 kB, ожидалось=130 kB; lsn=0/157F6E8, lsn redo=0/157F6A8
    2024-08-19 11:56:19.935 UTC [2322] postgres@postgres ОШИБКА:  обнаружена взаимоблокировка
    2024-08-19 11:56:19.935 UTC [2322] postgres@postgres ПОДРОБНОСТИ:  Процесс 2322 ожидает в режиме ShareLock блокировку "транзакция 751"; заблокирован процессом 2172.
            Процесс 2172 ожидает в режиме ShareLock блокировку "транзакция 752"; заблокирован процессом 2257.
            Процесс 2257 ожидает в режиме ShareLock блокировку "транзакция 753"; заблокирован процессом 2322.
            Процесс 2322: UPDATE accounts SET amount=amount+30 WHERE acc_no=1;
            Процесс 2172: UPDATE accounts SET amount=amount+10 WHERE acc_no=2;
            Процесс 2257: UPDATE accounts SET amount=amount+20 WHERE acc_no=3;
    2024-08-19 11:56:19.935 UTC [2322] postgres@postgres ПОДСКАЗКА:  Подробности запроса смотрите в протоколе сервера.
    2024-08-19 11:56:19.935 UTC [2322] postgres@postgres КОНТЕКСТ:  при изменении кортежа (0,1) в отношении "accounts"
    2024-08-19 11:56:19.935 UTC [2322] postgres@postgres ОПЕРАТОР:  UPDATE accounts SET amount=amount+30 WHERE acc_no=1;
    2024-08-19 11:56:19.935 UTC [2257] postgres@postgres СООБЩЕНИЕ:  продолжительность: 13585.445 мс, оператор: UPDATE accounts SET amount=amount+20 WHERE acc_no=3;
    2024-08-19 11:59:32.811 UTC [2172] postgres@postgres СООБЩЕНИЕ:  продолжительность: 222235.116 мс, оператор: UPDATE accounts SET amount=amount+10 WHERE acc_no=2;
    anton@postgres1:~$
    ```
    Смотря в логи в-целом можно догадаться, что конкретно произошло, но не зная логики выполняемого приложения понять почему - сложно
* Могут ли две транзакции, выполняющие единственную команду UPDATE одной и той же таблицы (без where), заблокировать друг друга?
  * Done
    Теоретически - могут, но для этого они должны выполнять обновление строк "в разном порядке" и быть запущены абсолютно одновременно.

Задание со звездочкой*
* Попробуйте воспроизвести такую ситуацию.
  * None

