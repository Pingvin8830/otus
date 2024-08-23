**Описание/Пошаговая инструкция выполнения домашнего задания:**
* развернуть виртуальную машину любым удобным способом
  * Done
* поставить на неё PostgreSQL 15 любым способом
  * Done
    ```
    anton@postgres1:~$ pg_lsclusters
    Ver Cluster Port Status Owner Data directory Log file
    anton@postgres1:~$ sudo pg_createcluster 15 main
    Creating new PostgreSQL cluster 15/main ...
    /usr/lib/postgresql/15/bin/initdb -D /var/lib/postgresql/15/main --auth-local peer --auth-host scram-sha-256 --no-instructions
    Файлы, относящиеся к этой СУБД, будут принадлежать пользователю "postgres".
    От его имени также будет запускаться процесс сервера.
    
    Кластер баз данных будет инициализирован с локалью "ru_RU.UTF-8".
    Кодировка БД по умолчанию, выбранная в соответствии с настройками: "UTF8".
    Выбрана конфигурация текстового поиска по умолчанию "russian".
    
    Контроль целостности страниц данных отключён.
    
    исправление прав для существующего каталога /var/lib/postgresql/15/main... ок
    создание подкаталогов... ок
    выбирается реализация динамической разделяемой памяти... posix
    выбирается значение max_connections по умолчанию... 100
    выбирается значение shared_buffers по умолчанию... 128MB
    выбирается часовой пояс по умолчанию... Etc/UTC
    создание конфигурационных файлов... ок
    выполняется подготовительный скрипт... ок
    выполняется заключительная инициализация... ок
    сохранение данных на диске... ок
    Ver Cluster Port Status Owner    Data directory              Log file
    15  main    5432 down   postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
    anton@postgres1:~$ sudo pg_ctlcluster 15 main start
    anton@postgres1:~$ pg_lsclusters
    Ver Cluster Port Status Owner    Data directory              Log file
    15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
    anton@postgres1:~$
    ```
* настроить кластер PostgreSQL 15 на максимальную производительность не обращая внимание на возможные проблемы с надежностью в случае аварийной перезагрузки виртуальной машины
* нагрузить кластер через утилиту через утилиту pgbench (https://postgrespro.ru/docs/postgrespro/14/pgbench)
* написать какого значения tps удалось достичь, показать какие параметры в какие значения устанавливали и почему
  * Тестирование конфигурации по-умолчанию
    ```
    anton@postgres1:~$ sudo su - postgres                                                                                                                                                                                       13:58:47 [28/516]
    [sudo] password for anton:
    postgres@postgres1:~$ psql -c "CREATE DATABASE pg_perf"
    CREATE DATABASE
    postgres@postgres1:~$ pgbench -i pg_perf
    dropping old tables...
    ЗАМЕЧАНИЕ:  таблица "pgbench_accounts" не существует, пропускается
    ЗАМЕЧАНИЕ:  таблица "pgbench_branches" не существует, пропускается
    ЗАМЕЧАНИЕ:  таблица "pgbench_history" не существует, пропускается
    ЗАМЕЧАНИЕ:  таблица "pgbench_tellers" не существует, пропускается
    creating tables...
    generating data (client-side)...
    100000 of 100000 tuples (100%) done (elapsed 0.04 s, remaining 0.00 s)
    vacuuming...
    creating primary keys...
    done in 0.10 s (drop tables 0.00 s, create tables 0.00 s, client-side generate 0.06 s, vacuum 0.02 s, primary keys 0.02 s).
    postgres@postgres1:~$ pgbench -c 10 -P 10 -T 600 pg_perf
    pgbench (15.7 (Ubuntu 15.7-1.pgdg22.04+1))
    starting vacuum...end.
    progress: 10.0 s, 2920.7 tps, lat 3.418 ms stddev 2.474, 0 failed
    progress: 20.0 s, 2986.4 tps, lat 3.348 ms stddev 2.263, 0 failed
    progress: 30.0 s, 3002.9 tps, lat 3.329 ms stddev 2.316, 0 failed
    progress: 40.0 s, 2970.2 tps, lat 3.367 ms stddev 2.273, 0 failed
    progress: 50.0 s, 2971.3 tps, lat 3.364 ms stddev 2.386, 0 failed
    progress: 60.0 s, 2988.9 tps, lat 3.345 ms stddev 2.333, 0 failed
    progress: 70.0 s, 3038.1 tps, lat 3.291 ms stddev 2.349, 0 failed
    progress: 80.0 s, 2996.4 tps, lat 3.337 ms stddev 2.264, 0 failed
    progress: 90.0 s, 2966.7 tps, lat 3.370 ms stddev 2.335, 0 failed
    progress: 100.0 s, 3017.2 tps, lat 3.314 ms stddev 2.299, 0 failed
    progress: 110.0 s, 3003.8 tps, lat 3.329 ms stddev 2.223, 0 failed
    progress: 120.0 s, 2994.1 tps, lat 3.339 ms stddev 2.407, 0 failed
    progress: 130.0 s, 2990.1 tps, lat 3.343 ms stddev 2.289, 0 failed
    progress: 140.0 s, 2982.4 tps, lat 3.353 ms stddev 2.446, 0 failed
    progress: 150.0 s, 2998.5 tps, lat 3.334 ms stddev 2.410, 0 failed
    progress: 160.0 s, 2963.1 tps, lat 3.375 ms stddev 2.410, 0 failed
    progress: 170.0 s, 3018.9 tps, lat 3.312 ms stddev 2.271, 0 failed
    progress: 180.0 s, 1247.6 tps, lat 8.015 ms stddev 7.922, 0 failed
    progress: 190.0 s, 2673.0 tps, lat 3.740 ms stddev 2.630, 0 failed
    progress: 200.0 s, 2259.5 tps, lat 4.425 ms stddev 2.958, 0 failed
    progress: 210.0 s, 2259.1 tps, lat 4.426 ms stddev 3.022, 0 failed
    progress: 220.0 s, 2259.4 tps, lat 4.425 ms stddev 3.034, 0 failed
    progress: 230.0 s, 2253.6 tps, lat 4.437 ms stddev 3.059, 0 failed
    progress: 240.0 s, 2260.8 tps, lat 4.422 ms stddev 3.022, 0 failed
    progress: 250.0 s, 2249.3 tps, lat 4.446 ms stddev 3.059, 0 failed
    progress: 260.0 s, 2261.0 tps, lat 4.422 ms stddev 2.965, 0 failed
    progress: 270.0 s, 2256.2 tps, lat 4.432 ms stddev 3.010, 0 failed
    progress: 280.0 s, 2257.6 tps, lat 4.429 ms stddev 3.090, 0 failed
    progress: 290.0 s, 2242.1 tps, lat 4.459 ms stddev 2.973, 0 failed
    progress: 300.0 s, 1786.2 tps, lat 5.595 ms stddev 5.741, 0 failed
    progress: 310.0 s, 2022.5 tps, lat 4.947 ms stddev 3.992, 0 failed
    progress: 320.0 s, 2179.8 tps, lat 4.586 ms stddev 3.139, 0 failed
    progress: 330.0 s, 2243.8 tps, lat 4.456 ms stddev 2.978, 0 failed
    progress: 340.0 s, 2239.6 tps, lat 4.464 ms stddev 2.929, 0 failed
    progress: 350.0 s, 2246.6 tps, lat 4.451 ms stddev 3.046, 0 failed
    progress: 360.0 s, 2244.5 tps, lat 4.455 ms stddev 3.120, 0 failed
    progress: 370.0 s, 2249.6 tps, lat 4.444 ms stddev 3.054, 0 failed
    progress: 380.0 s, 1908.0 tps, lat 5.240 ms stddev 5.490, 0 failed
    progress: 390.0 s, 2241.7 tps, lat 4.461 ms stddev 3.110, 0 failed
    progress: 400.0 s, 2248.5 tps, lat 4.446 ms stddev 2.901, 0 failed
    progress: 410.0 s, 2578.9 tps, lat 3.878 ms stddev 2.743, 0 failed
    progress: 420.0 s, 3009.8 tps, lat 3.322 ms stddev 2.248, 0 failed
    progress: 430.0 s, 2996.7 tps, lat 3.336 ms stddev 2.319, 0 failed
    progress: 440.0 s, 2986.1 tps, lat 3.349 ms stddev 2.219, 0 failed
    progress: 450.0 s, 2987.4 tps, lat 3.347 ms stddev 2.274, 0 failed
    progress: 460.0 s, 3003.0 tps, lat 3.330 ms stddev 2.262, 0 failed
    progress: 470.0 s, 2666.8 tps, lat 3.748 ms stddev 2.653, 0 failed
    progress: 480.0 s, 2270.4 tps, lat 4.404 ms stddev 2.894, 0 failed
    progress: 490.0 s, 2265.2 tps, lat 4.415 ms stddev 2.970, 0 failed
    progress: 500.0 s, 2255.1 tps, lat 4.434 ms stddev 3.069, 0 failed
    progress: 510.0 s, 2265.4 tps, lat 4.413 ms stddev 2.944, 0 failed
    progress: 520.0 s, 2265.0 tps, lat 4.413 ms stddev 2.913, 0 failed
    progress: 530.0 s, 2256.5 tps, lat 4.432 ms stddev 3.032, 0 failed
    progress: 540.0 s, 2270.0 tps, lat 4.404 ms stddev 2.953, 0 failed
    progress: 550.0 s, 2266.4 tps, lat 4.411 ms stddev 2.989, 0 failed
    progress: 560.0 s, 2265.0 tps, lat 4.414 ms stddev 3.030, 0 failed
    progress: 570.0 s, 2200.2 tps, lat 4.545 ms stddev 2.988, 0 failed
    progress: 580.0 s, 2243.1 tps, lat 4.458 ms stddev 2.939, 0 failed
    progress: 590.0 s, 2185.5 tps, lat 4.575 ms stddev 3.171, 0 failed
    progress: 600.0 s, 2214.2 tps, lat 4.515 ms stddev 3.040, 0 failed
    transaction type: <builtin: TPC-B (sort of)>
    scaling factor: 1
    query mode: simple
    number of clients: 10
    number of threads: 1
    maximum number of tries: 1
    duration: 600 s
    number of transactions actually processed: 1503514
    number of failed transactions: 0 (0.000%)
    latency average = 3.990 ms
    latency stddev = 2.989 ms
    initial connection time = 13.848 ms
    tps = 2505.843582 (without initial connection time)
    postgres@postgres1:~$
    ```
  * Тестирование конфигурации от pgtune
    ```
    postgres@postgres1:~$ echo "# DB Version: 15
    # OS Type: linux
    # DB Type: mixed
    # Total Memory (RAM): 4 GB
    # CPUs num: 2
    # Connections num: 20
    # Data Storage: san

    max_connections = 20
    shared_buffers = 1GB
    effective_cache_size = 3GB
    maintenance_work_mem = 256MB
    checkpoint_completion_target = 0.9
    wal_buffers = 16MB
    default_statistics_target = 100
    random_page_cost = 1.1
    effective_io_concurrency = 300
    work_mem = 13107kB
    huge_pages = off
    min_wal_size = 1GB
    max_wal_size = 4GB" > /etc/postgresql/15/main/conf.d/pgtune.conf
    postgres@postgres1:~$
    logout
    anton@postgres1:~$ sudo systemctl restart postgresql@15-main
    [sudo] password for anton:
    anton@postgres1:~$
    anton@postgres1:~$ sudo su - postgres                                                                                                                                                                                       15:22:15 [13/736]
    postgres@postgres1:~$ pgbench -c 10 -P 10 -T 600 pg_perf
    pgbench (15.7 (Ubuntu 15.7-1.pgdg22.04+1))
    starting vacuum...end.
    progress: 10.0 s, 2927.9 tps, lat 3.410 ms stddev 2.534, 0 failed
    progress: 20.0 s, 2949.2 tps, lat 3.390 ms stddev 2.607, 0 failed
    progress: 30.0 s, 2907.2 tps, lat 3.438 ms stddev 2.428, 0 failed
    progress: 40.0 s, 2870.6 tps, lat 3.484 ms stddev 2.492, 0 failed
    progress: 50.0 s, 2947.8 tps, lat 3.392 ms stddev 2.465, 0 failed
    progress: 60.0 s, 2785.3 tps, lat 3.590 ms stddev 3.016, 0 failed
    progress: 70.0 s, 2972.9 tps, lat 3.363 ms stddev 2.566, 0 failed
    progress: 80.0 s, 2974.0 tps, lat 3.362 ms stddev 2.453, 0 failed
    progress: 90.0 s, 2820.3 tps, lat 3.544 ms stddev 2.949, 0 failed
    progress: 100.0 s, 2673.4 tps, lat 3.741 ms stddev 3.415, 0 failed
    progress: 110.0 s, 2913.1 tps, lat 3.432 ms stddev 2.587, 0 failed
    progress: 120.0 s, 3015.2 tps, lat 3.316 ms stddev 2.521, 0 failed
    progress: 130.0 s, 3038.9 tps, lat 3.291 ms stddev 2.392, 0 failed
    progress: 140.0 s, 3024.0 tps, lat 3.306 ms stddev 2.224, 0 failed
    progress: 150.0 s, 2887.6 tps, lat 3.463 ms stddev 2.603, 0 failed
    progress: 160.0 s, 2856.4 tps, lat 3.500 ms stddev 2.672, 0 failed
    progress: 170.0 s, 2918.2 tps, lat 3.426 ms stddev 2.494, 0 failed
    progress: 180.0 s, 2989.6 tps, lat 3.345 ms stddev 2.395, 0 failed
    progress: 190.0 s, 2944.6 tps, lat 3.396 ms stddev 2.505, 0 failed
    progress: 200.0 s, 2956.6 tps, lat 3.382 ms stddev 2.387, 0 failed
    progress: 210.0 s, 2969.0 tps, lat 3.368 ms stddev 2.308, 0 failed
    progress: 220.0 s, 2840.9 tps, lat 3.519 ms stddev 2.385, 0 failed
    progress: 230.0 s, 2845.8 tps, lat 3.511 ms stddev 2.510, 0 failed
    progress: 240.0 s, 2904.8 tps, lat 3.443 ms stddev 2.519, 0 failed
    progress: 250.0 s, 2872.3 tps, lat 3.482 ms stddev 2.820, 0 failed
    progress: 260.0 s, 2977.1 tps, lat 3.358 ms stddev 2.430, 0 failed
    progress: 270.0 s, 2831.3 tps, lat 3.531 ms stddev 2.722, 0 failed
    progress: 280.0 s, 2579.3 tps, lat 3.877 ms stddev 3.552, 0 failed
    progress: 290.0 s, 2839.5 tps, lat 3.521 ms stddev 2.482, 0 failed
    progress: 300.0 s, 2816.9 tps, lat 3.549 ms stddev 2.510, 0 failed
    progress: 310.0 s, 2904.7 tps, lat 3.442 ms stddev 2.406, 0 failed
    progress: 320.0 s, 2968.7 tps, lat 3.367 ms stddev 2.306, 0 failed
    progress: 330.0 s, 2951.5 tps, lat 3.388 ms stddev 2.329, 0 failed
    progress: 340.0 s, 2895.2 tps, lat 3.453 ms stddev 2.634, 0 failed
    progress: 350.0 s, 2818.5 tps, lat 3.547 ms stddev 2.634, 0 failed
    progress: 360.0 s, 2546.0 tps, lat 3.927 ms stddev 4.202, 0 failed
    progress: 370.0 s, 2874.2 tps, lat 3.479 ms stddev 2.564, 0 failed
    progress: 380.0 s, 2825.9 tps, lat 3.538 ms stddev 2.528, 0 failed
    progress: 390.0 s, 2843.7 tps, lat 3.515 ms stddev 2.465, 0 failed
    progress: 400.0 s, 2887.3 tps, lat 3.463 ms stddev 2.451, 0 failed
    progress: 410.0 s, 2868.6 tps, lat 3.485 ms stddev 2.484, 0 failed
    progress: 420.0 s, 2902.1 tps, lat 3.445 ms stddev 2.365, 0 failed
    progress: 430.0 s, 2867.3 tps, lat 3.487 ms stddev 2.406, 0 failed
    progress: 440.0 s, 2843.5 tps, lat 3.516 ms stddev 2.427, 0 failed
    progress: 450.0 s, 2846.3 tps, lat 3.513 ms stddev 2.503, 0 failed
    progress: 460.0 s, 2982.2 tps, lat 3.353 ms stddev 2.431, 0 failed
    progress: 470.0 s, 2988.3 tps, lat 3.346 ms stddev 2.505, 0 failed
    progress: 480.0 s, 2955.2 tps, lat 3.383 ms stddev 2.405, 0 failed
    progress: 490.0 s, 2940.1 tps, lat 3.401 ms stddev 2.433, 0 failed
    progress: 500.0 s, 2974.4 tps, lat 3.361 ms stddev 2.300, 0 failed
    progress: 510.0 s, 2955.0 tps, lat 3.384 ms stddev 2.318, 0 failed
    progress: 520.0 s, 2971.9 tps, lat 3.364 ms stddev 2.336, 0 failed
    progress: 530.0 s, 2993.8 tps, lat 3.340 ms stddev 2.332, 0 failed
    progress: 540.0 s, 2962.3 tps, lat 3.375 ms stddev 2.293, 0 failed
    progress: 550.0 s, 2990.0 tps, lat 3.344 ms stddev 2.256, 0 failed
    progress: 560.0 s, 2964.9 tps, lat 3.373 ms stddev 2.311, 0 failed
    progress: 570.0 s, 2991.3 tps, lat 3.343 ms stddev 2.368, 0 failed
    progress: 580.0 s, 2965.9 tps, lat 3.370 ms stddev 2.401, 0 failed
    progress: 590.0 s, 2945.4 tps, lat 3.395 ms stddev 2.277, 0 failed
    progress: 600.0 s, 2972.5 tps, lat 3.364 ms stddev 2.314, 0 failed
    transaction type: <builtin: TPC-B (sort of)>
    scaling factor: 1
    query mode: simple
    number of clients: 10
    number of threads: 1
    maximum number of tries: 1
    duration: 600 s
    number of transactions actually processed: 1742474
    number of failed transactions: 0 (0.000%)
    latency average = 3.443 ms
    latency stddev = 2.544 ms
    initial connection time = 12.978 ms
    tps = 2904.114331 (without initial connection time)
    postgres@postgres1:~$
    ```
  * Тестирование кастомного конфига

    Сначала уменьшим количество обращений к подсистеме ввода-вывода
    ```
    postgres@postgres1:~$ echo "
    > listen_addresses = localhost
    > fsync = off
    > " > /etc/postgresql/15/main/conf.d/custom.conf
    postgres@postgres1:~$
    ```

    Далее отключим синхронный коммит, чтобы СУБД не ждала записи на диск для фиксации транзакций
    ```
    postgres@postgres1:~$ echo "
    > synchronous_commit = off
    > " >> /etc/postgresql/15/main/conf.d/custom.conf
    postgres@postgres1:~$
    ```

    Выкрутим checkpoint в максимально "неудобное" положение
    ```
    postgres@postgres1:~$ echo "
    > checkpoint_completion_target = 0
    > checkpoint_timeout = 86400
    > " >> /etc/postgresql/15/main/conf.d/custom.conf
    postgres@postgres1:~$ 
    ```

    Отключим avtovacuum
    ```
    postgres@postgres1:~$ echo "
    > autovacuum = off
    > " >> /etc/postgresql/15/main/conf.d/custom.conf
    postgres@postgres1:~$ 
    logout
    ```

    С учётом маленьких ресурсов ВМ, малого размера БД и отсутствия полезной нагрузки - остальные параметры существенного влияния на производительность не окажут

    Удалим конфиг для pgtune, перезапустим СУБД
    ```
    anton@postgres1:~$ sudo rm /etc/postgresql/15/main/conf.d/pgtune.conf
    [sudo] password for anton:
    anton@postgres1:~$ sudo systemctl restart postgresql@15-main
    anton@postgres1:~$
    ```

    Проверим конфигурацию
    ```
    anton@postgres1:~$ sudo su - postgres                                                                                                                                                                                      13:40:03 [57/1918]
    postgres@postgres1:~$ psql -c "DROP DATABASE IF EXISTS pg_perf"
    DROP DATABASE
    postgres@postgres1:~$ psql -c "CREATE DATABASE pg_perf"
    CREATE DATABASE
    postgres@postgres1:~$ pgbench -i pg_perf
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
    done in 0.07 s (drop tables 0.00 s, create tables 0.00 s, client-side generate 0.04 s, vacuum 0.02 s, primary keys 0.01 s).
    postgres@postgres1:~$ psql -c "CHECKPOINT;" pg_perf
    CHECKPOINT
    postgres@postgres1:~$ sleep 60
    postgres@postgres1:~$ pgbench -c 10 -P 10 -T 600 pg_perf
    pgbench (15.7 (Ubuntu 15.7-1.pgdg22.04+1))
    starting vacuum...end.
    progress: 10.0 s, 7171.6 tps, lat 1.392 ms stddev 0.702, 0 failed
    progress: 20.0 s, 7056.5 tps, lat 1.417 ms stddev 0.639, 0 failed
    progress: 30.0 s, 7025.5 tps, lat 1.423 ms stddev 1.015, 0 failed
    progress: 40.0 s, 6879.9 tps, lat 1.453 ms stddev 1.095, 0 failed
    progress: 50.0 s, 5751.3 tps, lat 1.738 ms stddev 0.968, 0 failed
    progress: 60.0 s, 5712.9 tps, lat 1.750 ms stddev 0.849, 0 failed
    progress: 70.0 s, 5763.2 tps, lat 1.734 ms stddev 0.893, 0 failed
    progress: 80.0 s, 5709.4 tps, lat 1.751 ms stddev 0.872, 0 failed
    progress: 90.0 s, 5722.0 tps, lat 1.747 ms stddev 0.864, 0 failed
    progress: 100.0 s, 5697.6 tps, lat 1.754 ms stddev 0.900, 0 failed
    progress: 110.0 s, 5758.3 tps, lat 1.736 ms stddev 0.913, 0 failed
    progress: 120.0 s, 6737.3 tps, lat 1.484 ms stddev 1.034, 0 failed
    progress: 130.0 s, 7128.8 tps, lat 1.402 ms stddev 0.980, 0 failed
    progress: 140.0 s, 7057.1 tps, lat 1.416 ms stddev 0.993, 0 failed
    progress: 150.0 s, 7101.7 tps, lat 1.407 ms stddev 1.036, 0 failed
    progress: 160.0 s, 7041.3 tps, lat 1.420 ms stddev 1.128, 0 failed
    progress: 170.0 s, 7080.1 tps, lat 1.412 ms stddev 1.050, 0 failed
    progress: 180.0 s, 6998.8 tps, lat 1.428 ms stddev 0.983, 0 failed
    progress: 190.0 s, 6956.2 tps, lat 1.437 ms stddev 1.109, 0 failed
    progress: 200.0 s, 7056.8 tps, lat 1.417 ms stddev 0.979, 0 failed
    progress: 210.0 s, 7057.6 tps, lat 1.416 ms stddev 1.004, 0 failed
    progress: 220.0 s, 7090.9 tps, lat 1.410 ms stddev 1.040, 0 failed
    progress: 230.0 s, 6974.2 tps, lat 1.433 ms stddev 1.140, 0 failed
    progress: 240.0 s, 6585.1 tps, lat 1.518 ms stddev 1.100, 0 failed
    progress: 250.0 s, 6820.1 tps, lat 1.466 ms stddev 1.013, 0 failed
    progress: 260.0 s, 6805.6 tps, lat 1.469 ms stddev 1.032, 0 failed
    progress: 270.0 s, 6838.5 tps, lat 1.462 ms stddev 0.993, 0 failed
    progress: 280.0 s, 6748.7 tps, lat 1.481 ms stddev 1.093, 0 failed
    progress: 290.0 s, 6833.0 tps, lat 1.463 ms stddev 1.034, 0 failed
    progress: 300.0 s, 6869.1 tps, lat 1.455 ms stddev 0.981, 0 failed
    progress: 310.0 s, 6764.9 tps, lat 1.478 ms stddev 0.994, 0 failed
    progress: 320.0 s, 6841.2 tps, lat 1.461 ms stddev 1.010, 0 failed
    progress: 330.0 s, 6759.5 tps, lat 1.479 ms stddev 1.054, 0 failed
    progress: 340.0 s, 6792.2 tps, lat 1.472 ms stddev 1.003, 0 failed
    progress: 350.0 s, 6871.2 tps, lat 1.455 ms stddev 1.008, 0 failed
    progress: 360.0 s, 6803.7 tps, lat 1.469 ms stddev 1.060, 0 failed
    progress: 370.0 s, 6852.9 tps, lat 1.459 ms stddev 1.052, 0 failed
    progress: 380.0 s, 6795.1 tps, lat 1.471 ms stddev 0.988, 0 failed
    progress: 390.0 s, 6765.4 tps, lat 1.478 ms stddev 1.056, 0 failed
    progress: 400.0 s, 6817.9 tps, lat 1.466 ms stddev 0.985, 0 failed
    progress: 410.0 s, 6815.3 tps, lat 1.466 ms stddev 1.073, 0 failed
    progress: 420.0 s, 6816.0 tps, lat 1.467 ms stddev 1.079, 0 failed
    progress: 430.0 s, 6797.0 tps, lat 1.471 ms stddev 1.046, 0 failed
    progress: 440.0 s, 6810.5 tps, lat 1.468 ms stddev 1.028, 0 failed
    progress: 450.0 s, 6761.6 tps, lat 1.478 ms stddev 1.058, 0 failed
    progress: 460.0 s, 6550.8 tps, lat 1.526 ms stddev 1.011, 0 failed
    progress: 470.0 s, 6702.8 tps, lat 1.491 ms stddev 1.037, 0 failed
    progress: 480.0 s, 6706.7 tps, lat 1.490 ms stddev 1.017, 0 failed
    progress: 490.0 s, 6680.4 tps, lat 1.496 ms stddev 1.039, 0 failed
    progress: 500.0 s, 6707.5 tps, lat 1.490 ms stddev 1.064, 0 failed
    progress: 510.0 s, 6724.2 tps, lat 1.487 ms stddev 1.013, 0 failed
    progress: 520.0 s, 6740.0 tps, lat 1.483 ms stddev 0.969, 0 failed
    progress: 530.0 s, 6656.0 tps, lat 1.502 ms stddev 1.097, 0 failed
    progress: 540.0 s, 6713.6 tps, lat 1.489 ms stddev 1.000, 0 failed
    progress: 550.0 s, 6743.0 tps, lat 1.482 ms stddev 1.016, 0 failed
    progress: 560.0 s, 6759.0 tps, lat 1.479 ms stddev 1.013, 0 failed
    progress: 570.0 s, 6756.6 tps, lat 1.479 ms stddev 0.902, 0 failed
    progress: 580.0 s, 6782.6 tps, lat 1.474 ms stddev 0.965, 0 failed
    progress: 590.0 s, 6763.3 tps, lat 1.478 ms stddev 0.990, 0 failed
    progress: 600.0 s, 6809.0 tps, lat 1.467 ms stddev 0.927, 0 failed
    transaction type: <builtin: TPC-B (sort of)>
    scaling factor: 1
    query mode: simple
    number of clients: 10
    number of threads: 1
    maximum number of tries: 1
    duration: 600 s
    number of transactions actually processed: 4028900
    number of failed transactions: 0 (0.000%)
    latency average = 1.489 ms
    latency stddev = 1.009 ms
    initial connection time = 12.433 ms
    tps = 6714.792507 (without initial connection time)
    postgres@postgres1:~$
    ```

