**Описание/Пошаговая инструкция выполнения домашнего задания:**
* Настройте выполнение контрольной точки раз в 30 секунд.
  * Done
    ```
    anton@postgres1:~$ sudo su -
    [sudo] password for anton:
    root@postgres1:~# pg_createcluster 16 otus_hw_06
    Creating new PostgreSQL cluster 16/otus_hw_06 ...
    /usr/lib/postgresql/16/bin/initdb -D /var/lib/postgresql/16/otus_hw_06 --auth-local peer --auth-host scram-sha-256 --no-instructions
    Файлы, относящиеся к этой СУБД, будут принадлежать пользователю "postgres".
    От его имени также будет запускаться процесс сервера.

    Кластер баз данных будет инициализирован с локалью "ru_RU.UTF-8".
    Кодировка БД по умолчанию, выбранная в соответствии с настройками: "UTF8".
    Выбрана конфигурация текстового поиска по умолчанию "russian".

    Контроль целостности страниц данных отключён.

    исправление прав для существующего каталога /var/lib/postgresql/16/otus_hw_06... ок
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
    16  otus_hw_06 5433 down   postgres /var/lib/postgresql/16/otus_hw_06 /var/log/postgresql/postgresql-16-otus_hw_06.log
    root@postgres1:~# pg_ctlcluster 16 otus_hw_06 start
    root@postgres1:~# pg_ctlcluster 16 otus_hw_06 status
    pg_ctl: сервер работает (PID: 2815)
    /usr/lib/postgresql/16/bin/postgres "-D" "/var/lib/postgresql/16/otus_hw_06" "-c" "config_file=/etc/postgresql/16/otus_hw_06/postgresql.conf"
    root@postgres1:~# echo "checkpoint_timeout = 30" > /etc/postgresql/16/otus_hw_06/conf.d/checkpoint.conf
    root@postgres1:~# chown postgres:postgres /etc/postgresql/16/otus_hw_06/conf.d/checkpoint.conf
    root@postgres1:~# systemctl reload postgresql@16-otus_hw_06
    root@postgres1:~# su - postgres -c "psql -c 'SHOW checkpoint_timeout;'"
     checkpoint_timeout
    --------------------
     30s
    (1 строка)

    root@postgres1:~#
    ```
* 10 минут c помощью утилиты pgbench подавайте нагрузку.
  * Done
    ```
    root@postgres1:~# su - postgres                                                                                                                                                                                             22:19:50 [65/397]
    postgres@postgres1:~$ pgbench -i postgres -p 5433
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
    done in 0.11 s (drop tables 0.00 s, create tables 0.00 s, client-side generate 0.05 s, vacuum 0.02 s, primary keys 0.03 s).
    postgres@postgres1:~$ pgbench -p 5433 -c8 -P 6 -T 600 postgres
    pgbench (16.3 (Ubuntu 16.3-1.pgdg22.04+1))
    starting vacuum...end.
    progress: 6.0 s, 2811.2 tps, lat 2.839 ms stddev 1.965, 0 failed
    progress: 12.0 s, 2857.3 tps, lat 2.799 ms stddev 1.990, 0 failed
    progress: 18.0 s, 2921.2 tps, lat 2.739 ms stddev 1.837, 0 failed
    progress: 24.0 s, 2897.2 tps, lat 2.761 ms stddev 1.803, 0 failed
    progress: 30.0 s, 2894.0 tps, lat 2.763 ms stddev 1.909, 0 failed
    progress: 36.0 s, 2913.7 tps, lat 2.745 ms stddev 1.877, 0 failed
    progress: 42.0 s, 2881.4 tps, lat 2.776 ms stddev 1.844, 0 failed
    progress: 48.0 s, 2920.3 tps, lat 2.739 ms stddev 1.920, 0 failed
    progress: 54.0 s, 2930.2 tps, lat 2.729 ms stddev 1.808, 0 failed
    progress: 60.0 s, 2862.5 tps, lat 2.794 ms stddev 1.810, 0 failed
    progress: 66.0 s, 2811.0 tps, lat 2.844 ms stddev 1.926, 0 failed
    progress: 72.0 s, 2901.7 tps, lat 2.757 ms stddev 1.818, 0 failed
    progress: 78.0 s, 2884.7 tps, lat 2.773 ms stddev 1.899, 0 failed
    progress: 84.0 s, 2880.0 tps, lat 2.777 ms stddev 1.892, 0 failed
    progress: 90.0 s, 2855.5 tps, lat 2.801 ms stddev 1.960, 0 failed
    progress: 96.0 s, 2945.8 tps, lat 2.715 ms stddev 1.794, 0 failed
    progress: 102.0 s, 2966.7 tps, lat 2.696 ms stddev 1.804, 0 failed
    progress: 108.0 s, 2982.8 tps, lat 2.681 ms stddev 1.806, 0 failed
    progress: 114.0 s, 2939.6 tps, lat 2.721 ms stddev 1.860, 0 failed
    progress: 120.0 s, 2981.2 tps, lat 2.683 ms stddev 1.758, 0 failed
    progress: 126.0 s, 2974.0 tps, lat 2.689 ms stddev 1.869, 0 failed
    progress: 132.0 s, 2913.1 tps, lat 2.745 ms stddev 1.863, 0 failed
    progress: 138.0 s, 2937.0 tps, lat 2.723 ms stddev 1.801, 0 failed
    progress: 144.0 s, 2982.2 tps, lat 2.682 ms stddev 1.767, 0 failed
    progress: 150.0 s, 2962.8 tps, lat 2.699 ms stddev 1.833, 0 failed
    progress: 156.0 s, 2932.7 tps, lat 2.727 ms stddev 1.790, 0 failed
    progress: 162.0 s, 2952.3 tps, lat 2.709 ms stddev 1.757, 0 failed
    progress: 168.0 s, 2949.0 tps, lat 2.712 ms stddev 1.841, 0 failed
    progress: 174.0 s, 2947.0 tps, lat 2.714 ms stddev 1.807, 0 failed
    progress: 180.0 s, 2961.0 tps, lat 2.701 ms stddev 1.954, 0 failed
    progress: 186.0 s, 2957.0 tps, lat 2.705 ms stddev 1.853, 0 failed
    progress: 192.0 s, 2965.8 tps, lat 2.697 ms stddev 1.748, 0 failed
    progress: 198.0 s, 2995.7 tps, lat 2.670 ms stddev 1.784, 0 failed
    progress: 204.0 s, 2888.7 tps, lat 2.769 ms stddev 1.896, 0 failed
    progress: 210.0 s, 2904.8 tps, lat 2.753 ms stddev 1.834, 0 failed
    progress: 216.0 s, 2973.2 tps, lat 2.690 ms stddev 1.761, 0 failed
    progress: 222.0 s, 2899.0 tps, lat 2.759 ms stddev 1.878, 0 failed
    progress: 228.0 s, 2987.5 tps, lat 2.677 ms stddev 1.766, 0 failed
    progress: 234.0 s, 2975.9 tps, lat 2.688 ms stddev 1.837, 0 failed
    progress: 240.0 s, 2965.0 tps, lat 2.698 ms stddev 1.874, 0 failed
    progress: 246.0 s, 2952.4 tps, lat 2.709 ms stddev 1.815, 0 failed
    progress: 252.0 s, 3011.8 tps, lat 2.655 ms stddev 1.808, 0 failed
    progress: 258.0 s, 2997.5 tps, lat 2.669 ms stddev 1.717, 0 failed
    progress: 264.0 s, 2975.7 tps, lat 2.688 ms stddev 1.851, 0 failed
    progress: 270.0 s, 2985.8 tps, lat 2.678 ms stddev 1.832, 0 failed
    progress: 276.0 s, 2985.8 tps, lat 2.680 ms stddev 1.776, 0 failed
    progress: 282.0 s, 2975.5 tps, lat 2.688 ms stddev 1.814, 0 failed
    progress: 288.0 s, 2834.0 tps, lat 2.822 ms stddev 1.882, 0 failed
    progress: 294.0 s, 2863.1 tps, lat 2.793 ms stddev 1.907, 0 failed
    progress: 300.0 s, 2898.8 tps, lat 2.759 ms stddev 1.848, 0 failed
    progress: 306.0 s, 2982.7 tps, lat 2.682 ms stddev 1.828, 0 failed
    progress: 312.0 s, 2990.7 tps, lat 2.674 ms stddev 1.819, 0 failed
    progress: 318.0 s, 3000.7 tps, lat 2.666 ms stddev 1.774, 0 failed
    progress: 324.0 s, 2983.8 tps, lat 2.680 ms stddev 1.814, 0 failed
    progress: 330.0 s, 2939.5 tps, lat 2.721 ms stddev 1.780, 0 failed
    progress: 336.0 s, 3003.0 tps, lat 2.663 ms stddev 1.804, 0 failed
    progress: 342.0 s, 2998.2 tps, lat 2.668 ms stddev 1.723, 0 failed
    progress: 348.0 s, 2958.4 tps, lat 2.703 ms stddev 1.767, 0 failed
    progress: 354.0 s, 2964.0 tps, lat 2.698 ms stddev 1.798, 0 failed
    progress: 360.0 s, 2986.3 tps, lat 2.679 ms stddev 1.705, 0 failed
    progress: 366.0 s, 2919.5 tps, lat 2.739 ms stddev 1.820, 0 failed
    progress: 372.0 s, 2936.8 tps, lat 2.724 ms stddev 1.810, 0 failed
    progress: 378.0 s, 2936.5 tps, lat 2.723 ms stddev 1.789, 0 failed
    progress: 384.0 s, 2923.7 tps, lat 2.736 ms stddev 1.923, 0 failed
    progress: 390.0 s, 2923.0 tps, lat 2.737 ms stddev 1.895, 0 failed
    progress: 396.0 s, 2972.2 tps, lat 2.691 ms stddev 1.843, 0 failed
    progress: 402.0 s, 2970.3 tps, lat 2.692 ms stddev 1.816, 0 failed
    progress: 408.0 s, 2968.5 tps, lat 2.695 ms stddev 1.730, 0 failed
    progress: 414.0 s, 2956.0 tps, lat 2.706 ms stddev 1.865, 0 failed
    progress: 420.0 s, 2891.7 tps, lat 2.766 ms stddev 1.875, 0 failed
    progress: 426.0 s, 2916.2 tps, lat 2.743 ms stddev 1.930, 0 failed
    progress: 432.0 s, 2819.5 tps, lat 2.837 ms stddev 1.887, 0 failed
    progress: 438.0 s, 2901.5 tps, lat 2.756 ms stddev 1.788, 0 failed
    progress: 444.0 s, 2954.3 tps, lat 2.708 ms stddev 1.767, 0 failed
    progress: 450.0 s, 2895.5 tps, lat 2.762 ms stddev 1.949, 0 failed
    progress: 456.0 s, 2846.5 tps, lat 2.810 ms stddev 1.956, 0 failed
    progress: 462.0 s, 2814.3 tps, lat 2.842 ms stddev 1.998, 0 failed
    progress: 468.0 s, 2888.5 tps, lat 2.769 ms stddev 2.013, 0 failed
    progress: 474.0 s, 2918.2 tps, lat 2.741 ms stddev 1.900, 0 failed
    progress: 480.0 s, 2959.2 tps, lat 2.703 ms stddev 1.777, 0 failed
    progress: 486.0 s, 2963.5 tps, lat 2.699 ms stddev 1.824, 0 failed
    progress: 492.0 s, 2873.0 tps, lat 2.784 ms stddev 1.942, 0 failed
    progress: 498.0 s, 2968.2 tps, lat 2.695 ms stddev 1.732, 0 failed
    progress: 504.0 s, 2946.2 tps, lat 2.715 ms stddev 1.783, 0 failed
    progress: 510.0 s, 2946.8 tps, lat 2.714 ms stddev 1.885, 0 failed
    progress: 516.0 s, 2931.2 tps, lat 2.729 ms stddev 1.858, 0 failed
    progress: 522.0 s, 2980.6 tps, lat 2.683 ms stddev 1.848, 0 failed
    progress: 528.0 s, 2967.0 tps, lat 2.695 ms stddev 1.844, 0 failed
    progress: 534.0 s, 2931.7 tps, lat 2.729 ms stddev 1.897, 0 failed
    progress: 540.0 s, 2947.3 tps, lat 2.714 ms stddev 1.835, 0 failed
    progress: 546.0 s, 2922.3 tps, lat 2.737 ms stddev 1.883, 0 failed
    progress: 552.0 s, 2969.6 tps, lat 2.693 ms stddev 1.836, 0 failed
    progress: 558.0 s, 2955.0 tps, lat 2.706 ms stddev 1.823, 0 failed
    progress: 564.0 s, 2910.3 tps, lat 2.748 ms stddev 1.856, 0 failed
    progress: 570.0 s, 2963.3 tps, lat 2.699 ms stddev 1.803, 0 failed
    progress: 576.0 s, 2928.5 tps, lat 2.732 ms stddev 1.843, 0 failed
    progress: 582.0 s, 2957.3 tps, lat 2.704 ms stddev 1.824, 0 failed
    progress: 588.0 s, 2964.8 tps, lat 2.699 ms stddev 1.959, 0 failed
    progress: 594.0 s, 2975.0 tps, lat 2.688 ms stddev 1.814, 0 failed
    progress: 600.0 s, 2974.0 tps, lat 2.690 ms stddev 1.775, 0 failed
    transaction type: <builtin: TPC-B (sort of)>
    scaling factor: 1
    query mode: simple
    number of clients: 8
    number of threads: 1
    maximum number of tries: 1
    duration: 600 s
    number of transactions actually processed: 1761831
    number of failed transactions: 0 (0.000%)
    latency average = 2.724 ms
    latency stddev = 1.841 ms
    initial connection time = 11.151 ms
    tps = 2936.392821 (without initial connection time)
    postgres@postgres1:~$
    ```
* Измерьте, какой объем журнальных файлов был сгенерирован за это время. Оцените, какой объем приходится в среднем на одну контрольную точку.
  * Done
    ```
    postgres@postgres1:~$ du -sh /var/lib/postgresql/16/otus_hw_06/pg_wal
    129M    /var/lib/postgresql/16/otus_hw_06/pg_wal
    postgres@postgres1:~$
    ```
* Проверьте данные статистики: все ли контрольные точки выполнялись точно по расписанию. Почему так произошло?
  * Done
    ```
    postgres@postgres1:~$ grep "начата контрольная точка: time" /var/log/postgresql/postgresql-16-otus_hw_06.log
    2024-07-22 19:13:25.700 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:14:55.608 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:15:25.083 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:15:55.046 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:16:25.019 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:16:55.075 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:17:25.047 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:17:55.103 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:18:25.075 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:18:55.035 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:19:25.103 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:19:55.059 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:20:25.031 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:20:55.095 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:21:25.063 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:21:55.035 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:22:25.103 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:22:55.063 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:23:25.027 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:23:55.083 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:24:25.044 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:24:55.007 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    2024-07-22 19:25:55.068 UTC [2816] СООБЩЕНИЕ:  начата контрольная точка: time
    postgres@postgres1:~$
    ```
    В-целом - да, раз в 30 секунд. Разница времени запуска в несколько миллисекунд. Скорее всего из-за незавершённых записей в WAL
* Сравните tps в синхронном/асинхронном режиме утилитой pgbench. Объясните полученный результат.
  * Done
    ```
    root@postgres1:~# su - postgres -c "psql -p 5433 -c 'SHOW synchronous_commit;'"
     synchronous_commit
    --------------------
     on
    (1 строка)

    root@postgres1:~# echo "synchronous_commit = off" > /etc/postgresql/16/otus_hw_06/conf.d/wal.conf
    root@postgres1:~# chown postgres:postgres /etc/postgresql/16/otus_hw_06/conf.d/wal.conf
    root@postgres1:~# systemctl reload postgresql@16-otus_hw_06
    root@postgres1:~# su - postgres -c "psql -p 5433 -c 'SHOW synchronous_commit;'"
     synchronous_commit
    --------------------
     off
    (1 строка)

    root@postgres1:~# su - postgres                                                                                                                                                                                             22:50:21 [53/611]
    postgres@postgres1:~$ pgbench -p 5433 -c8 -P 6 -T 600 postgres
    pgbench (16.3 (Ubuntu 16.3-1.pgdg22.04+1))
    starting vacuum...end.
    progress: 6.0 s, 7069.7 tps, lat 1.129 ms stddev 0.544, 0 failed
    progress: 12.0 s, 7082.3 tps, lat 1.129 ms stddev 0.476, 0 failed
    progress: 18.0 s, 7118.1 tps, lat 1.123 ms stddev 0.475, 0 failed
    progress: 24.0 s, 7007.4 tps, lat 1.141 ms stddev 0.580, 0 failed
    progress: 30.0 s, 7083.0 tps, lat 1.129 ms stddev 0.515, 0 failed
    progress: 36.0 s, 7012.8 tps, lat 1.140 ms stddev 0.551, 0 failed
    progress: 42.0 s, 7039.7 tps, lat 1.136 ms stddev 0.533, 0 failed
    progress: 48.0 s, 7094.5 tps, lat 1.127 ms stddev 0.516, 0 failed
    progress: 54.0 s, 7102.7 tps, lat 1.126 ms stddev 0.512, 0 failed
    progress: 60.0 s, 6965.6 tps, lat 1.148 ms stddev 0.534, 0 failed
    progress: 66.0 s, 6929.7 tps, lat 1.154 ms stddev 0.654, 0 failed
    progress: 72.0 s, 6985.0 tps, lat 1.145 ms stddev 0.586, 0 failed
    progress: 78.0 s, 6994.7 tps, lat 1.143 ms stddev 0.567, 0 failed
    progress: 84.0 s, 6894.3 tps, lat 1.160 ms stddev 0.589, 0 failed
    progress: 90.0 s, 6924.3 tps, lat 1.155 ms stddev 0.581, 0 failed
    progress: 96.0 s, 6974.2 tps, lat 1.146 ms stddev 0.591, 0 failed
    progress: 102.0 s, 6977.9 tps, lat 1.146 ms stddev 0.611, 0 failed
    progress: 108.0 s, 7037.2 tps, lat 1.136 ms stddev 0.582, 0 failed
    progress: 114.0 s, 7132.0 tps, lat 1.121 ms stddev 0.545, 0 failed
    progress: 120.0 s, 7230.5 tps, lat 1.106 ms stddev 0.540, 0 failed
    progress: 126.0 s, 7242.0 tps, lat 1.104 ms stddev 0.567, 0 failed
    progress: 132.0 s, 7095.3 tps, lat 1.127 ms stddev 0.583, 0 failed
    progress: 138.0 s, 7138.5 tps, lat 1.120 ms stddev 0.607, 0 failed
    progress: 144.0 s, 7104.5 tps, lat 1.125 ms stddev 0.598, 0 failed
    progress: 150.0 s, 7132.0 tps, lat 1.121 ms stddev 0.596, 0 failed
    progress: 156.0 s, 7114.6 tps, lat 1.124 ms stddev 0.584, 0 failed
    progress: 162.0 s, 7155.0 tps, lat 1.118 ms stddev 0.565, 0 failed
    progress: 168.0 s, 6863.4 tps, lat 1.165 ms stddev 0.617, 0 failed
    progress: 174.0 s, 6989.2 tps, lat 1.144 ms stddev 0.636, 0 failed
    progress: 180.0 s, 6856.1 tps, lat 1.166 ms stddev 0.668, 0 failed
    progress: 186.0 s, 6934.5 tps, lat 1.153 ms stddev 0.653, 0 failed
    progress: 192.0 s, 7039.8 tps, lat 1.136 ms stddev 0.608, 0 failed
    progress: 198.0 s, 6974.5 tps, lat 1.146 ms stddev 0.588, 0 failed
    progress: 204.0 s, 7030.5 tps, lat 1.137 ms stddev 0.603, 0 failed
    progress: 210.0 s, 7071.0 tps, lat 1.131 ms stddev 0.570, 0 failed
    progress: 216.0 s, 6952.8 tps, lat 1.150 ms stddev 0.644, 0 failed
    progress: 222.0 s, 6939.3 tps, lat 1.152 ms stddev 0.630, 0 failed
    progress: 228.0 s, 7136.0 tps, lat 1.121 ms stddev 0.630, 0 failed
    progress: 234.0 s, 7053.0 tps, lat 1.134 ms stddev 0.650, 0 failed
    progress: 240.0 s, 7175.0 tps, lat 1.114 ms stddev 0.591, 0 failed
    progress: 246.0 s, 7180.9 tps, lat 1.113 ms stddev 0.587, 0 failed
    progress: 252.0 s, 7121.8 tps, lat 1.123 ms stddev 0.584, 0 failed
    progress: 258.0 s, 7158.1 tps, lat 1.117 ms stddev 0.547, 0 failed
    progress: 264.0 s, 7091.5 tps, lat 1.128 ms stddev 0.606, 0 failed
    progress: 270.0 s, 7173.6 tps, lat 1.115 ms stddev 0.610, 0 failed
    progress: 276.0 s, 7061.8 tps, lat 1.132 ms stddev 0.581, 0 failed
    progress: 282.0 s, 7133.5 tps, lat 1.121 ms stddev 0.610, 0 failed
    progress: 288.0 s, 6880.6 tps, lat 1.162 ms stddev 0.648, 0 failed
    progress: 294.0 s, 7024.5 tps, lat 1.138 ms stddev 0.578, 0 failed
    progress: 300.0 s, 7044.5 tps, lat 1.135 ms stddev 0.617, 0 failed
    progress: 306.0 s, 6956.3 tps, lat 1.149 ms stddev 0.614, 0 failed
    progress: 312.0 s, 6944.5 tps, lat 1.151 ms stddev 0.594, 0 failed
    progress: 318.0 s, 6924.8 tps, lat 1.155 ms stddev 0.578, 0 failed
    progress: 324.0 s, 7027.3 tps, lat 1.138 ms stddev 0.594, 0 failed
    progress: 330.0 s, 6966.9 tps, lat 1.148 ms stddev 0.598, 0 failed
    progress: 336.0 s, 6911.8 tps, lat 1.157 ms stddev 0.612, 0 failed
    progress: 342.0 s, 6993.0 tps, lat 1.144 ms stddev 0.666, 0 failed
    progress: 348.0 s, 7028.8 tps, lat 1.138 ms stddev 0.591, 0 failed
    progress: 354.0 s, 7161.3 tps, lat 1.117 ms stddev 0.573, 0 failed
    progress: 360.0 s, 7208.3 tps, lat 1.109 ms stddev 0.594, 0 failed
    progress: 366.0 s, 7133.7 tps, lat 1.121 ms stddev 0.590, 0 failed
    progress: 372.0 s, 7186.0 tps, lat 1.113 ms stddev 0.571, 0 failed
    progress: 378.0 s, 7177.8 tps, lat 1.114 ms stddev 0.559, 0 failed
    progress: 384.0 s, 7103.0 tps, lat 1.126 ms stddev 0.594, 0 failed
    progress: 390.0 s, 7167.5 tps, lat 1.116 ms stddev 0.567, 0 failed
    progress: 396.0 s, 7226.7 tps, lat 1.106 ms stddev 0.614, 0 failed
    progress: 402.0 s, 7178.0 tps, lat 1.114 ms stddev 0.645, 0 failed
    progress: 408.0 s, 6880.2 tps, lat 1.162 ms stddev 0.655, 0 failed
    progress: 414.0 s, 6972.8 tps, lat 1.147 ms stddev 0.652, 0 failed
    progress: 420.0 s, 6921.5 tps, lat 1.155 ms stddev 0.639, 0 failed
    progress: 426.0 s, 6903.0 tps, lat 1.158 ms stddev 0.660, 0 failed
    progress: 432.0 s, 6928.7 tps, lat 1.154 ms stddev 0.604, 0 failed
    progress: 438.0 s, 6994.7 tps, lat 1.143 ms stddev 0.623, 0 failed
    progress: 444.0 s, 6965.0 tps, lat 1.148 ms stddev 0.574, 0 failed
    progress: 450.0 s, 6955.1 tps, lat 1.150 ms stddev 0.621, 0 failed
    progress: 456.0 s, 6980.7 tps, lat 1.145 ms stddev 0.643, 0 failed
    progress: 462.0 s, 6951.2 tps, lat 1.150 ms stddev 0.663, 0 failed
    progress: 468.0 s, 6871.5 tps, lat 1.164 ms stddev 0.617, 0 failed
    progress: 474.0 s, 6895.8 tps, lat 1.159 ms stddev 0.604, 0 failed
    progress: 480.0 s, 7006.2 tps, lat 1.141 ms stddev 0.629, 0 failed
    progress: 486.0 s, 6963.2 tps, lat 1.149 ms stddev 0.581, 0 failed
    progress: 492.0 s, 6949.2 tps, lat 1.151 ms stddev 0.568, 0 failed
    progress: 498.0 s, 6970.7 tps, lat 1.147 ms stddev 0.601, 0 failed
    progress: 504.0 s, 7013.5 tps, lat 1.140 ms stddev 0.603, 0 failed
    progress: 510.0 s, 6883.3 tps, lat 1.162 ms stddev 0.645, 0 failed
    progress: 516.0 s, 6969.9 tps, lat 1.147 ms stddev 0.591, 0 failed
    progress: 522.0 s, 6958.3 tps, lat 1.149 ms stddev 0.629, 0 failed
    progress: 528.0 s, 6822.0 tps, lat 1.172 ms stddev 0.647, 0 failed
    progress: 534.0 s, 7114.8 tps, lat 1.124 ms stddev 0.561, 0 failed
    progress: 540.0 s, 7010.3 tps, lat 1.140 ms stddev 0.602, 0 failed
    progress: 546.0 s, 7064.5 tps, lat 1.132 ms stddev 0.594, 0 failed
    progress: 552.0 s, 7077.9 tps, lat 1.130 ms stddev 0.597, 0 failed
    progress: 558.0 s, 7063.2 tps, lat 1.132 ms stddev 0.584, 0 failed
    progress: 564.0 s, 6979.7 tps, lat 1.146 ms stddev 0.607, 0 failed
    progress: 570.0 s, 7081.3 tps, lat 1.129 ms stddev 0.604, 0 failed
    progress: 576.0 s, 6972.2 tps, lat 1.147 ms stddev 0.648, 0 failed
    progress: 582.0 s, 6874.2 tps, lat 1.163 ms stddev 0.686, 0 failed
    progress: 588.0 s, 6926.3 tps, lat 1.155 ms stddev 0.657, 0 failed
    progress: 594.0 s, 7249.5 tps, lat 1.103 ms stddev 0.563, 0 failed
    progress: 600.0 s, 7099.9 tps, lat 1.126 ms stddev 0.579, 0 failed
    transaction type: <builtin: TPC-B (sort of)>
    scaling factor: 1
    query mode: simple
    number of clients: 8
    number of threads: 1
    maximum number of tries: 1
    duration: 600 s
    number of transactions actually processed: 4218546
    number of failed transactions: 0 (0.000%)
    latency average = 1.137 ms
    latency stddev = 0.599 ms
    initial connection time = 10.331 ms
    tps = 7030.933753 (without initial connection time)
    postgres@postgres1:~$
    ```
    Сравнивая результаты двух запусков pgbench видим разницу более, чем в 2 раза.
    tps = 2936.392821 (without initial connection time)
    tps = 7030.933753 (without initial connection time)
    
    TPS в асинхронном режиме ожидаемо выше, т.к. СУБД не ждёт записи на диск
* Создайте новый кластер с включенной контрольной суммой страниц. Создайте таблицу. Вставьте несколько значений. Выключите кластер. Измените пару байт в таблице. Включите кластер и сделайте выборку из таблицы. Что и почему произошло? как проигнорировать ошибку и продолжить работу?
  * Done
    ```
    postgres@postgres1:~$
    logout
    root@postgres1:~# pg_createcluster 16 checksums -- --data-checksums                                                                                                                                                        12:55:21 [43/1023]
    Creating new PostgreSQL cluster 16/checksums ...
    /usr/lib/postgresql/16/bin/initdb -D /var/lib/postgresql/16/checksums --auth-local peer --auth-host scram-sha-256 --no-instructions --data-checksums
    Файлы, относящиеся к этой СУБД, будут принадлежать пользователю "postgres".
    От его имени также будет запускаться процесс сервера.

    Кластер баз данных будет инициализирован с локалью "ru_RU.UTF-8".
    Кодировка БД по умолчанию, выбранная в соответствии с настройками: "UTF8".
    Выбрана конфигурация текстового поиска по умолчанию "russian".

    Контроль целостности страниц данных включён.

    исправление прав для существующего каталога /var/lib/postgresql/16/checksums... ок
    создание подкаталогов... ок
    выбирается реализация динамической разделяемой памяти... posix
    выбирается значение max_connections по умолчанию... 100
    выбирается значение shared_buffers по умолчанию... 128MB
    выбирается часовой пояс по умолчанию... Etc/UTC
    создание конфигурационных файлов... ок
    выполняется подготовительный скрипт... ок
    выполняется заключительная инициализация... ок
    сохранение данных на диске... ок
    Ver Cluster   Port Status Owner    Data directory                   Log file
    16  checksums 5434 down   postgres /var/lib/postgresql/16/checksums /var/log/postgresql/postgresql-16-checksums.log
    root@postgres1:~# pg_ctlcluster 16 checksums start
    root@postgres1:~# su - postgres -c "psql -p 5434"
    psql (16.3 (Ubuntu 16.3-1.pgdg22.04+1))
    Введите "help", чтобы получить справку.

    postgres=# SHOW data_checksums;
     data_checksums
    ----------------
     on
    (1 строка)

    postgres=# SHOW ignore_checksum_failure;
     ignore_checksum_failure
    -------------------------
     off
    (1 строка)

    postgres=# CREATE TABLE control (dt TIMESTAMP);
    CREATE TABLE
    postgres=# INSERT INTO control VALUES (NOW());
    INSERT 0 1
    postgres=# INSERT INTO control VALUES (NOW());
    INSERT 0 1
    postgres=# INSERT INTO control VALUES (NOW());
    INSERT 0 1
    postgres=# INSERT INTO control VALUES (NOW());
    INSERT 0 1
    postgres=# INSERT INTO control VALUES (NOW());
    INSERT 0 1
    postgres=# SELECT * FROM control;
                 dt
    ----------------------------
     2024-07-23 09:54:59.140048
     2024-07-23 09:55:00.617888
     2024-07-23 09:55:01.480205
     2024-07-23 09:55:02.431862
     2024-07-23 09:55:03.391431
    (5 строк)

    postgres=# SELECT pg_relation_filepath('control');
     pg_relation_filepath
    ----------------------
     base/5/16384
    (1 строка)

    postgres=#
    \q
    root@postgres1:~# pg_ctlcluster 16 checksums stop
    root@postgres1:~# pg_lsclusters
    Ver Cluster    Port Status Owner    Data directory                    Log file
    16  checksums  5434 down   postgres /var/lib/postgresql/16/checksums  /var/log/postgresql/postgresql-16-checksums.log
    16  main       5432 online postgres /var/lib/postgresql/16/main       /var/log/postgresql/postgresql-16-main.log
    16  otus_hw_06 5433 online postgres /var/lib/postgresql/16/otus_hw_06 /var/log/postgresql/postgresql-16-otus_hw_06.log
    root@postgres1:~# vim /var/lib/postgresql/16/checksums/base/5/16384
    root@postgres1:~# pg_ctlcluster 16 checksums start
    root@postgres1:~# su - postgres -c "psql -p 5434"
    psql (16.3 (Ubuntu 16.3-1.pgdg22.04+1))
    Введите "help", чтобы получить справку.

    postgres=# SELECT * FROM control;
     dt
    ----
    (0 строк)

    postgres=#
    \q
    root@postgres1:~# echo "ignore_checksum_failure = on" > /etc/postgresql/16/checksums/conf.d/checksums.conf
    root@postgres1:~# pg_ctlcluster 16 checksums restart
    root@postgres1:~# su - postgres -c "psql -p 5434"
    psql (16.3 (Ubuntu 16.3-1.pgdg22.04+1))
    Введите "help", чтобы получить справку.

    postgres=# SELECT * FROM control;
     dt
    ----
    (0 строк)
  
    postgres=# SHOW ignore_checksum_failure;
     ignore_checksum_failure
    -------------------------
     on
    (1 строка)

    postgres=# \d control
                                       Таблица "public.control"
     Столбец |             Тип             | Правило сортировки | Допустимость NULL | По умолчанию
    ---------+-----------------------------+--------------------+-------------------+--------------
     dt      | timestamp without time zone |                    |                   |

    postgres=#
    ```
    Не понял :-(

    Кластер создался, чексуммы включились. При изменении файла таблицы - запрос отрабатывает, возвращает пустой результат, структуру таблицы видно.

