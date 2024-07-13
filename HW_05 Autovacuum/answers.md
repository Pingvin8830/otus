**Описание/Пошаговая инструкция выполнения домашнего задания:**
* Создать инстанс ВМ с 2 ядрами и 4 Гб ОЗУ и SSD 10GB
  * Done
    ```
    anton@postgres1:~$ lscpu | grep 'CPU(s)'
    CPU(s):                             2
    On-line CPU(s) list:                0,1
    NUMA node0 CPU(s):                  0,1
    anton@postgres1:~$ free -h
                   total        used        free      shared  buff/cache   available
    Mem:           3,8Gi       196Mi       3,4Gi        13Mi       252Mi       3,4Gi
    Swap:             0B          0B          0B
    anton@postgres1:~$ lsblk
    NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
    loop0                       7:0    0 63,9M  1 loop /snap/core20/2318
    loop1                       7:1    0   87M  1 loop /snap/lxd/27037
    loop2                       7:2    0   87M  1 loop /snap/lxd/28373
    loop3                       7:3    0 63,9M  1 loop /snap/core20/2105
    loop4                       7:4    0 40,4M  1 loop /snap/snapd/20671
    loop5                       7:5    0 38,8M  1 loop /snap/snapd/21759
    sda                         8:0    0   10G  0 disk
    ├─sda1                      8:1    0  538M  0 part /boot/efi
    ├─sda2                      8:2    0  1,8G  0 part /boot
    └─sda3                      8:3    0  7,7G  0 part
      └─ubuntu--vg-ubuntu--lv 253:0    0  7,7G  0 lvm  /
    sr0                        11:0    1 1024M  0 rom
    anton@postgres1:~$
    ```
* Установить на него PostgreSQL 15 с дефолтными настройками
  * Done
    ```
    anton@postgres1:~$ sudo pg_createcluster 15 autovacuum
    [sudo] password for anton:
    Creating new PostgreSQL cluster 15/autovacuum ...
    /usr/lib/postgresql/15/bin/initdb -D /var/lib/postgresql/15/autovacuum --auth-local peer --auth-host scram-sha-256 --no-instructions
    Файлы, относящиеся к этой СУБД, будут принадлежать пользователю "postgres".
    От его имени также будет запускаться процесс сервера.
  
    Кластер баз данных будет инициализирован с локалью "ru_RU.UTF-8".
    Кодировка БД по умолчанию, выбранная в соответствии с настройками: "UTF8".
    Выбрана конфигурация текстового поиска по умолчанию "russian".
  
    Контроль целостности страниц данных отключён.
  
    исправление прав для существующего каталога /var/lib/postgresql/15/autovacuum... ок
    создание подкаталогов... ок
    выбирается реализация динамической разделяемой памяти... posix
    выбирается значение max_connections по умолчанию... 100
    выбирается значение shared_buffers по умолчанию... 128MB
    выбирается часовой пояс по умолчанию... Etc/UTC
    создание конфигурационных файлов... ок
    выполняется подготовительный скрипт... ок
    выполняется заключительная инициализация... ок
    сохранение данных на диске... ок
    Ver Cluster    Port Status Owner    Data directory                    Log file
    15  autovacuum 5433 down   postgres /var/lib/postgresql/15/autovacuum /var/log/postgresql/postgresql-15-autovacuum.log
    anton@postgres1:~$ sudo pg_ctlcluster start 15 autovacuum
    anton@postgres1:~$ pg_lsclusters
    Ver Cluster    Port Status Owner    Data directory                    Log file
    15  autovacuum 5433 online postgres /var/lib/postgresql/15/autovacuum /var/log/postgresql/postgresql-15-autovacuum.log
    15  main       5432 online postgres /var/lib/postgresql/15/main       /var/log/postgresql/postgresql-15-main.log
    anton@postgres1:~$ sudo pg_ctlcluster stop 15 main
    anton@postgres1:~$ pg_lsclusters
    Ver Cluster    Port Status Owner    Data directory                    Log file
    15  autovacuum 5433 online postgres /var/lib/postgresql/15/autovacuum /var/log/postgresql/postgresql-15-autovacuum.log
    15  main       5432 down   postgres /var/lib/postgresql/15/main       /var/log/postgresql/postgresql-15-main.log
    anton@postgres1:~$
    ```
* Создать БД для тестов: выполнить pgbench -i postgres
  * Done
    ```
    anton@postgres1:~$ sudo su - postgres
    postgres@postgres1:~$ pgbench -p 5433 -i postgres
    dropping old tables...
    ЗАМЕЧАНИЕ:  таблица "pgbench_accounts" не существует, пропускается
    ЗАМЕЧАНИЕ:  таблица "pgbench_branches" не существует, пропускается
    ЗАМЕЧАНИЕ:  таблица "pgbench_history" не существует, пропускается
    ЗАМЕЧАНИЕ:  таблица "pgbench_tellers" не существует, пропускается
    creating tables...
    generating data (client-side)...
    100000 of 100000 tuples (100%) done (elapsed 0.03 s, remaining 0.00 s)
    vacuuming...
    creating primary keys...
    done in 0.11 s (drop tables 0.00 s, create tables 0.00 s, client-side generate 0.05 s, vacuum 0.02 s, primary keys 0.04 s).
    postgres@postgres1:~$
    ```
* Запустить pgbench -c8 -P 6 -T 60 -U postgres postgres
  * Done
    ```
    postgres@postgres1:~$ pgbench -p 5433 -c8 -P 6 -T 60 -U postgres postgres
    pgbench (15.7 (Ubuntu 15.7-1.pgdg22.04+1))
    starting vacuum...end.
    progress: 6.0 s, 2877.7 tps, lat 2.774 ms stddev 1.918, 0 failed
    progress: 12.0 s, 2934.0 tps, lat 2.725 ms stddev 1.954, 0 failed
    progress: 18.0 s, 2918.0 tps, lat 2.741 ms stddev 1.891, 0 failed
    progress: 24.0 s, 2946.0 tps, lat 2.715 ms stddev 1.873, 0 failed
    progress: 30.0 s, 2945.7 tps, lat 2.715 ms stddev 1.793, 0 failed
    progress: 36.0 s, 2922.8 tps, lat 2.736 ms stddev 1.910, 0 failed
    progress: 42.0 s, 2938.7 tps, lat 2.722 ms stddev 1.906, 0 failed
    progress: 48.0 s, 2916.0 tps, lat 2.743 ms stddev 1.874, 0 failed
    progress: 54.0 s, 2924.0 tps, lat 2.735 ms stddev 1.851, 0 failed
    progress: 60.0 s, 2921.3 tps, lat 2.738 ms stddev 1.940, 0 failed
    transaction type: <builtin: TPC-B (sort of)>
    scaling factor: 1
    query mode: simple
    number of clients: 8
    number of threads: 1
    maximum number of tries: 1
    duration: 60 s
    number of transactions actually processed: 175472
    number of failed transactions: 0 (0.000%)
    latency average = 2.735 ms
    latency stddev = 1.892 ms
    initial connection time = 9.088 ms
    tps = 2924.450571 (without initial connection time)
    postgres@postgres1:~$
    ```
* Применить параметры настройки PostgreSQL из прикрепленного к материалам занятия файла
  * Done
    ```
    postgres@postgres1:~$ exit
    logout
    anton@postgres1:~$ sudo su -
    root@postgres1:~# vim /etc/postgresql/15/autovacuum/conf.d/homework.conf
    root@postgres1:~# chown postgres:postgres /etc/postgresql/15/autovacuum/conf.d/homework.conf
    root@postgres1:~# cat /etc/postgresql/15/autovacuum/conf.d/homework.conf
    # DB Version: 11
    # OS Type: linux
    # DB Type: dw
    # Total Memory (RAM): 4 GB
    # CPUs num: 1
    # Data Storage: hdd

    max_connections = 40
    shared_buffers = 1GB
    effective_cache_size = 3GB
    maintenance_work_mem = 512MB
    checkpoint_completion_target = 0.9
    wal_buffers = 16MB
    default_statistics_target = 500
    random_page_cost = 4
    effective_io_concurrency = 2
    work_mem = 6553kB
    min_wal_size = 4GB
    max_wal_size = 16GB
    root@postgres1:~# systemctl restart postgresql@15-autovacuum
    root@postgres1:~# su - postgres
    postgres@postgres1:~$ psql -p 5433 -c "SHOW max_connections"
     max_connections
    -----------------
     40
    (1 строка)

    postgres@postgres1:~$
    ```
* Протестировать заново
  * Done
    ```
    postgres@postgres1:~$ pgbench -p 5433 -c8 -P 6 -T 60 -U postgres postgres
    pgbench (15.7 (Ubuntu 15.7-1.pgdg22.04+1))
    starting vacuum...end.
    progress: 6.0 s, 2865.3 tps, lat 2.786 ms stddev 1.868, 0 failed
    progress: 12.0 s, 2951.7 tps, lat 2.710 ms stddev 1.905, 0 failed
    progress: 18.0 s, 2937.8 tps, lat 2.722 ms stddev 1.755, 0 failed
    progress: 24.0 s, 2937.7 tps, lat 2.723 ms stddev 1.812, 0 failed
    progress: 30.0 s, 2921.3 tps, lat 2.738 ms stddev 1.871, 0 failed
    progress: 36.0 s, 2925.5 tps, lat 2.734 ms stddev 1.959, 0 failed
    progress: 42.0 s, 2967.3 tps, lat 2.695 ms stddev 1.891, 0 failed
    progress: 48.0 s, 2920.7 tps, lat 2.738 ms stddev 1.847, 0 failed
    progress: 54.0 s, 2958.8 tps, lat 2.704 ms stddev 1.814, 0 failed
    progress: 60.0 s, 2947.5 tps, lat 2.713 ms stddev 1.819, 0 failed
    transaction type: <builtin: TPC-B (sort of)>
    scaling factor: 1
    query mode: simple
    number of clients: 8
    number of threads: 1
    maximum number of tries: 1
    duration: 60 s
    number of transactions actually processed: 176010
    number of failed transactions: 0 (0.000%)
    latency average = 2.726 ms
    latency stddev = 1.855 ms
    initial connection time = 10.013 ms
    tps = 2933.556960 (without initial connection time)
    postgres@postgres1:~$
    ```
* Что изменилось и почему?
  * Изменилась производительность СУБД (количество транзакций, обрабатываемых в секунду). Потому что основные параметры, отвечающие за работу изменились. В данном примере - незначительно, т.к. параметры ВМ невысокие. Повторные запуски теста показывают дальнейший рост производительности из-за накопления данных в буфере и уменьшения количества IO операций
    ```
    postgres@postgres1:~$ pgbench -p 5433 -c8 -P 6 -T 60 -U postgres postgres
    pgbench (15.7 (Ubuntu 15.7-1.pgdg22.04+1))
    starting vacuum...end.
    progress: 6.0 s, 2865.8 tps, lat 2.785 ms stddev 1.944, 0 failed
    progress: 12.0 s, 2926.2 tps, lat 2.733 ms stddev 1.999, 0 failed
    progress: 18.0 s, 2943.8 tps, lat 2.717 ms stddev 1.873, 0 failed
    progress: 24.0 s, 2945.2 tps, lat 2.715 ms stddev 1.909, 0 failed
    progress: 30.0 s, 2961.3 tps, lat 2.701 ms stddev 1.762, 0 failed
    progress: 36.0 s, 2925.7 tps, lat 2.734 ms stddev 1.952, 0 failed
    progress: 42.0 s, 2956.3 tps, lat 2.705 ms stddev 1.846, 0 failed
    progress: 48.0 s, 2981.3 tps, lat 2.683 ms stddev 1.833, 0 failed
    progress: 54.0 s, 2964.3 tps, lat 2.698 ms stddev 1.900, 0 failed
    progress: 60.0 s, 2974.5 tps, lat 2.689 ms stddev 1.882, 0 failed
    transaction type: <builtin: TPC-B (sort of)>
    scaling factor: 1
    query mode: simple
    number of clients: 8
    number of threads: 1
    maximum number of tries: 1
    duration: 60 s
    number of transactions actually processed: 176675
    number of failed transactions: 0 (0.000%)
    latency average = 2.716 ms
    latency stddev = 1.891 ms
    initial connection time = 11.117 ms
    tps = 2944.636828 (without initial connection time)
    postgres@postgres1:~$ pgbench -p 5433 -c8 -P 6 -T 60 -U postgres postgres
    pgbench (15.7 (Ubuntu 15.7-1.pgdg22.04+1))
    starting vacuum...end.
    progress: 6.0 s, 2942.1 tps, lat 2.713 ms stddev 1.777, 0 failed
    progress: 12.0 s, 2899.8 tps, lat 2.758 ms stddev 1.890, 0 failed
    progress: 18.0 s, 2940.7 tps, lat 2.720 ms stddev 1.842, 0 failed
    progress: 24.0 s, 2927.7 tps, lat 2.732 ms stddev 1.822, 0 failed
    progress: 30.0 s, 2934.8 tps, lat 2.725 ms stddev 1.792, 0 failed
    progress: 36.0 s, 2966.0 tps, lat 2.696 ms stddev 1.748, 0 failed
    progress: 42.0 s, 2960.3 tps, lat 2.702 ms stddev 1.857, 0 failed
    progress: 48.0 s, 2996.3 tps, lat 2.669 ms stddev 1.781, 0 failed
    progress: 54.0 s, 2955.4 tps, lat 2.706 ms stddev 1.857, 0 failed
    progress: 60.0 s, 2947.9 tps, lat 2.714 ms stddev 1.841, 0 failed
    transaction type: <builtin: TPC-B (sort of)>
    scaling factor: 1
    query mode: simple
    number of clients: 8
    number of threads: 1
    maximum number of tries: 1
    duration: 60 s
    number of transactions actually processed: 176834
    number of failed transactions: 0 (0.000%)
    latency average = 2.714 ms
    latency stddev = 1.821 ms
    initial connection time = 10.183 ms
    tps = 2947.292377 (without initial connection time)
    postgres@postgres1:~$
    ```
* Создать таблицу с текстовым полем и заполнить случайными или сгенерированными данным в размере 1млн строк
  * Done
    ```
    postgres=# CREATE TABLE performance(value text);
    CREATE TABLE
    postgres=# INSERT INTO performance (value) SELECT 'noname' FROM generate_series(1,1000000);
    INSERT 0 1000000
    postgres=#
    ```
* Посмотреть размер файла с таблицей
  * Done
    ```
    postgres=# SELECT pg_size_pretty(pg_total_relation_size('performance'));
     pg_size_pretty
    ----------------
     35 MB
    (1 строка)

    postgres=# SELECT pg_relation_filepath('performance');
     pg_relation_filepath
    ----------------------
     base/5/16454
    (1 строка)
    
    postgres=# \q
    postgres@postgres1:~$ du -sh /var/lib/postgresql/15/autovacuum/base/5/16454
    35M     /var/lib/postgresql/15/autovacuum/base/5/16454
    postgres@postgres1:~$
    ```
* 5 раз обновить все строчки и добавить к каждой строчке любой символ
  * Done
    ```
    postgres=# UPDATE performance SET value=value || '1';
    UPDATE 1000000
    postgres=# UPDATE performance SET value=value || '2';
    UPDATE 1000000
    postgres=# UPDATE performance SET value=value || '3';
    UPDATE 1000000
    postgres=# UPDATE performance SET value=value || '4';
    UPDATE 1000000
    postgres=# UPDATE performance SET value=value || '5';
    UPDATE 1000000
    postgres=#
    ```
* Посмотреть количество мертвых строчек в таблице и когда последний раз приходил автовакуум
  * Done
    ```
    postgres=# SELECT n_dead_tup, last_autovacuum FROM pg_stat_user_tables WHERE relname='performance';
     n_dead_tup |        last_autovacuum
    ------------+-------------------------------
              0 | 2024-07-13 11:21:00.885368+00
    (1 строка)

    postgres=#
    ```

    Не успел... :-( Ещё раз
    ```
    postgres=# UPDATE performance SET value=value || '6';
    UPDATE 1000000
    postgres=# UPDATE performance SET value=value || '7';
    UPDATE 1000000
    postgres=# UPDATE performance SET value=value || '8';
    UPDATE 1000000
    postgres=# UPDATE performance SET value=value || '9';
    UPDATE 1000000
    postgres=# UPDATE performance SET value=value || '1';
    UPDATE 1000000
    postgres=# SELECT n_dead_tup, last_autovacuum FROM pg_stat_user_tables WHERE relname='performance';
     n_dead_tup |        last_autovacuum
    ------------+-------------------------------
        1999919 | 2024-07-13 11:22:00.733767+00
    (1 строка)

    postgres=#
    ```

* Подождать некоторое время, проверяя, пришел ли автовакуум
  * Done
    ```
    postgres=# SELECT n_dead_tup, last_autovacuum FROM pg_stat_user_tables WHERE relname='performance';
     n_dead_tup |        last_autovacuum
    ------------+-------------------------------
        1999919 | 2024-07-13 11:22:00.733767+00
    (1 строка)

    postgres=# SELECT n_dead_tup, last_autovacuum FROM pg_stat_user_tables WHERE relname='performance';
     n_dead_tup |        last_autovacuum
    ------------+-------------------------------
              0 | 2024-07-13 11:23:00.563866+00
    (1 строка)

    postgres=#
    ```
* 5 раз обновить все строчки и добавить к каждой строчке любой символ
  * Done
* Посмотреть размер файла с таблицей
  * Done
    ```
    postgres=# SELECT pg_size_pretty(pg_total_relation_size('performance'));
     pg_size_pretty
    ----------------
     238 MB
    (1 строка)

    postgres=# \q
    postgres@postgres1:~$ du -sh /var/lib/postgresql/15/autovacuum/base/5/16454
    239M    /var/lib/postgresql/15/autovacuum/base/5/16454
    postgres@postgres1:~$
    ```
* Отключить Автовакуум на конкретной таблице
  * Done
    ```
    postgres=# ALTER TABLE performance SET (autovacuum_enabled=off);
    ALTER TABLE
    postgres=#
    ```
* 10 раз обновить все строчки и добавить к каждой строчке любой символ
  * Done
    ```
    postgres=# UPDATE performance SET value=value || 'a';
    UPDATE 1000000
    postgres=# UPDATE performance SET value=value || 'b';
    UPDATE 1000000
    postgres=# UPDATE performance SET value=value || 'c';
    UPDATE 1000000
    postgres=# UPDATE performance SET value=value || 'd';
    UPDATE 1000000
    postgres=# UPDATE performance SET value=value || 'e';
    UPDATE 1000000
    postgres=# UPDATE performance SET value=value || 'f';
    UPDATE 1000000
    postgres=# UPDATE performance SET value=value || 'g';
    UPDATE 1000000
    postgres=# UPDATE performance SET value=value || 'h';
    UPDATE 1000000
    postgres=# UPDATE performance SET value=value || 'i';
    UPDATE 1000000
    postgres=# UPDATE performance SET value=value || 'j';
    UPDATE 1000000
    postgres=#
    ```
* Посмотреть размер файла с таблицей
  * Done
    ```
    postgres=# SELECT pg_size_pretty(pg_total_relation_size('performance'));
     pg_size_pretty
    ----------------
     570 MB
    (1 строка)

    postgres=#
    ```
* Объясните полученный результат
  * Размер таблицы постепенно увеличивается из-за накопления "мёртвых строк". При этом после процедуры очистки СУБД не всегда может использовать свободное место на страницах, т.к. размер ново версии строки может превышать размер "дырки". Также накапливается фрагментация, приводящая к увеличению IO операций и, соответственно - снижению производительности СУБД
* Не забудьте включить автовакуум)
  * Done
    ```
    postgres=# ALTER TABLE performance SET (autovacuum_enabled=on);
    ALTER TABLE
    postgres=#
    ```

Задание со *:
Написать анонимную процедуру, в которой в цикле 10 раз обновятся все строчки в искомой таблице.
Не забыть вывести номер шага цикла.
  ```
    postgres=# DO $$
    postgres$# BEGIN
    postgres$# FOR i IN 1..10 LOOP
    postgres$# RAISE NOTICE 'l: %', i;
    postgres$# UPDATE performance SET value=value||i;
    postgres$# END LOOP;
    postgres$# END; $$ ;
    ЗАМЕЧАНИЕ:  l: 1
    ЗАМЕЧАНИЕ:  l: 2
    ЗАМЕЧАНИЕ:  l: 3
    ЗАМЕЧАНИЕ:  l: 4
    ЗАМЕЧАНИЕ:  l: 5
    ЗАМЕЧАНИЕ:  l: 6
    ЗАМЕЧАНИЕ:  l: 7
    ЗАМЕЧАНИЕ:  l: 8
    ЗАМЕЧАНИЕ:  l: 9
    ЗАМЕЧАНИЕ:  l: 10
    DO
    postgres=# SELECT value FROM performance LIMIT 5;
                     value
    ---------------------------------------
     noname1234567891abcdefghij12345678910
     noname1234567891abcdefghij12345678910
     noname1234567891abcdefghij12345678910
     noname1234567891abcdefghij12345678910
     noname1234567891abcdefghij12345678910
    (5 строк)

    postgres=#
  ```
