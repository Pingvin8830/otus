**Описание/Пошаговая инструкция выполнения домашнего задания:**
* На 1 ВМ создаем таблицы test для записи, test2 для запросов на чтение.
* Создаем публикацию таблицы test и подписываемся на публикацию таблицы test2 с ВМ №2.
* На 2 ВМ создаем таблицы test2 для записи, test для запросов на чтение.
* Создаем публикацию таблицы test2 и подписываемся на публикацию таблицы test1 с ВМ №1.
* 3 ВМ использовать как реплику для чтения и бэкапов (подписаться на таблицы из ВМ №1 и №2 ).
* * реализовать горячее реплицирование для высокой доступности на 4ВМ. Источником должна выступать ВМ №3. Написать с какими проблемами столкнулись.

**Выполнение**

**Создание кластеров данных**
  * На ВМ 1 создаём кластер данных PostgreSQL 14
    ```
      [anton@manager ~]$ ssh postgres1.otus
      anton@postgres1:~$
      anton@postgres1:~$ sudo pg_createcluster 14 replication
      [sudo] password for anton:
      Creating new PostgreSQL cluster 14/replication ...
      /usr/lib/postgresql/14/bin/initdb -D /var/lib/postgresql/14/replication --auth-local peer --auth-host scram-sha-256 --no-instructions
      Файлы, относящиеся к этой СУБД, будут принадлежать пользователю "postgres".
      От его имени также будет запускаться процесс сервера.
    
      Кластер баз данных будет инициализирован с локалью "ru_RU.UTF-8".
      Кодировка БД по умолчанию, выбранная в соответствии с настройками: "UTF8".
      Выбрана конфигурация текстового поиска по умолчанию "russian".
    
      Контроль целостности страниц данных отключён.
    
      исправление прав для существующего каталога /var/lib/postgresql/14/replication... ок
      создание подкаталогов... ок
      выбирается реализация динамической разделяемой памяти... posix
      выбирается значение max_connections по умолчанию... 100
      выбирается значение shared_buffers по умолчанию... 128MB
      выбирается часовой пояс по умолчанию... Etc/UTC
      создание конфигурационных файлов... ок
      выполняется подготовительный скрипт... ок
      выполняется заключительная инициализация... ок
      сохранение данных на диске... ок
      Ver Cluster     Port Status Owner    Data directory                     Log file
      14  replication 5432 down   postgres /var/lib/postgresql/14/replication /var/log/postgresql/postgresql-14-replication.log
      anton@postgres1:~$
      anton@postgres1:~$ sudo pg_ctlcluster start 14 replication
      anton@postgres1:~$ pg_lsclusters
      Ver Cluster     Port Status Owner    Data directory                     Log file
      14  replication 5432 online postgres /var/lib/postgresql/14/replication /var/log/postgresql/postgresql-14-replication.log
      anton@postgres1:~$
      anton@postgres1:~$
      logout
      Connection to postgres1.otus closed.
      [anton@manager ~]$
    ```
  * На ВМ 2 создаём кластер данных PostgreSQL 15
    ```
      [anton@manager ~]$ ssh postgres2.otus
      anton@postgres2:~$
      anton@postgres2:~$ sudo pg_createcluster 15 replication
      [sudo] password for anton:
      Creating new PostgreSQL cluster 15/replication ...
      /usr/lib/postgresql/15/bin/initdb -D /var/lib/postgresql/15/replication --auth-local peer --auth-host scram-sha-256 --no-instructions
      Файлы, относящиеся к этой СУБД, будут принадлежать пользователю "postgres".
      От его имени также будет запускаться процесс сервера.
      
      Кластер баз данных будет инициализирован с локалью "ru_RU.UTF-8".
      Кодировка БД по умолчанию, выбранная в соответствии с настройками: "UTF8".
      Выбрана конфигурация текстового поиска по умолчанию "russian".
      
      Контроль целостности страниц данных отключён.
      
      исправление прав для существующего каталога /var/lib/postgresql/15/replication... ок
      создание подкаталогов... ок
      выбирается реализация динамической разделяемой памяти... posix
      выбирается значение max_connections по умолчанию... 100
      выбирается значение shared_buffers по умолчанию... 128MB
      выбирается часовой пояс по умолчанию... Etc/UTC
      создание конфигурационных файлов... ок
      выполняется подготовительный скрипт... ок
      выполняется заключительная инициализация... ок
      сохранение данных на диске... ок
      Ver Cluster     Port Status Owner    Data directory                     Log file
      15  replication 5432 down   postgres /var/lib/postgresql/15/replication /var/log/postgresql/postgresql-15-replication.log
      anton@postgres2:~$
      anton@postgres2:~$ sudo pg_ctlcluster 15 replication start
      anton@postgres2:~$ pg_lsclusters
      Ver Cluster     Port Status Owner    Data directory                     Log file
      15  replication 5432 online postgres /var/lib/postgresql/15/replication /var/log/postgresql/postgresql-15-replication.log
      anton@postgres2:~$
      anton@postgres2:~$
      logout
      Connection to postgres2.otus closed.
      [anton@manager ~]$
    ```
  * На ВМ 3 создаём кластер данных PostgreSQL 16
    ```
      [anton@manager ~]$ ssh postgres3.otus
      anton@postres3:~$
      anton@postres3:~$ sudo pg_createcluster 16 replication
      [sudo] password for anton:
      Creating new PostgreSQL cluster 16/replication ...
      /usr/lib/postgresql/16/bin/initdb -D /var/lib/postgresql/16/replication --auth-local peer --auth-host scram-sha-256 --no-instructions
      Файлы, относящиеся к этой СУБД, будут принадлежать пользователю "postgres".
      От его имени также будет запускаться процесс сервера.
      
      Кластер баз данных будет инициализирован с локалью "ru_RU.UTF-8".
      Кодировка БД по умолчанию, выбранная в соответствии с настройками: "UTF8".
      Выбрана конфигурация текстового поиска по умолчанию "russian".
      
      Контроль целостности страниц данных отключён.
      
      исправление прав для существующего каталога /var/lib/postgresql/16/replication... ок
      создание подкаталогов... ок
      выбирается реализация динамической разделяемой памяти... posix
      выбирается значение max_connections по умолчанию... 100
      выбирается значение shared_buffers по умолчанию... 128MB
      выбирается часовой пояс по умолчанию... Etc/UTC
      создание конфигурационных файлов... ок
      выполняется подготовительный скрипт... ок
      выполняется заключительная инициализация... ок
      сохранение данных на диске... ок
      Ver Cluster     Port Status Owner    Data directory                     Log file
      16  replication 5432 down   postgres /var/lib/postgresql/16/replication /var/log/postgresql/postgresql-16-replication.log
      anton@postres3:~$
      anton@postres3:~$ sudo pg_ctlcluster 16 replication start
      anton@postres3:~$ pg_lsclusters
      Ver Cluster     Port Status Owner    Data directory                     Log file
      16  replication 5432 online postgres /var/lib/postgresql/16/replication /var/log/postgresql/postgresql-16-replication.log
      anton@postres3:~$
      anton@postres3:~$
      logout
      Connection to postgres3.otus closed.
      [anton@manager ~]$
    ```
  * На ВМ 4 создаём кластер данных PostgreSQL 16
    ```
      [anton@manager ~]$ ssh postgres4.otus
      anton@postgres4:~$
      anton@postgres4:~$ sudo pg_createcluster 16 replication
      [sudo] password for anton:
      Creating new PostgreSQL cluster 16/replication ...
      /usr/lib/postgresql/16/bin/initdb -D /var/lib/postgresql/16/replication --auth-local peer --auth-host scram-sha-256 --no-instructions
      Файлы, относящиеся к этой СУБД, будут принадлежать пользователю "postgres".
      От его имени также будет запускаться процесс сервера.
      
      Кластер баз данных будет инициализирован с локалью "ru_RU.UTF-8".
      Кодировка БД по умолчанию, выбранная в соответствии с настройками: "UTF8".
      Выбрана конфигурация текстового поиска по умолчанию "russian".
      
      Контроль целостности страниц данных отключён.
      
      исправление прав для существующего каталога /var/lib/postgresql/16/replication... ок
      создание подкаталогов... ок
      выбирается реализация динамической разделяемой памяти... posix
      выбирается значение max_connections по умолчанию... 100
      выбирается значение shared_buffers по умолчанию... 128MB
      выбирается часовой пояс по умолчанию... Etc/UTC
      создание конфигурационных файлов... ок
      выполняется подготовительный скрипт... ок
      выполняется заключительная инициализация... ок
      сохранение данных на диске... ок
      Ver Cluster     Port Status Owner    Data directory                     Log file
      16  replication 5432 down   postgres /var/lib/postgresql/16/replication /var/log/postgresql/postgresql-16-replication.log
      anton@postgres4:~$
      anton@postgres4:~$ sudo pg_ctlcluster 16 replication start
      anton@postgres4:~$ pg_lsclusters
      Ver Cluster     Port Status Owner    Data directory                     Log file
      16  replication 5432 online postgres /var/lib/postgresql/16/replication /var/log/postgresql/postgresql-16-replication.log
      anton@postgres4:~$
      anton@postgres4:~$
      logout
      Connection to postgres4.otus closed.
      [anton@manager ~]$
    ```
**Разрешаем сетевые подключения к кластерам данных**
  * ВМ 1
    ```
      [anton@manager ~]$ ssh postgres1.otus
      anton@postgres1:~$ sudo su -
      [sudo] password for anton:
      root@postgres1:~#
      root@postgres1:~# echo "listen_addresses = '*'" > /etc/postgresql/14/replication/conf.d/hw_10.conf
      root@postgres1:~#
      root@postgres1:~#
      logout
      anton@postgres1:~$
      logout
      Connection to postgres1.otus closed.
      [anton@manager ~]$
    ```
  * ВМ 2
    ```
      [anton@manager ~]$ ssh postgres2.otus
      anton@postgres2:~$ sudo su -
      [sudo] password for anton:
      root@postgres2:~#
      root@postgres2:~# echo "listen_addresses = '*'" > /etc/postgresql/15/replication/conf.d/hw_10.conf
      root@postgres2:~#
      root@postgres2:~#
      logout
      anton@postgres2:~$
      logout
      Connection to postgres2.otus closed.
      [anton@manager ~]$
    ```
  * ВМ 3
    ```
      [anton@manager ~]$ ssh postgres3.otus
      anton@postres3:~$ sudo su -
      [sudo] password for anton:
      root@postres3:~#
      root@postres3:~# echo "listen_addresses = '*'" > /etc/postgresql/16/replication/conf.d/hw_10.conf
      root@postres3:~#
      root@postres3:~#
      logout
      anton@postres3:~$
      logout
      Connection to postgres3.otus closed.
      [anton@manager ~]$
    ```
  * ВМ 4
    ```
      [anton@manager ~]$ ssh postgres4.otus
      anton@postgres4:~$ sudo su -
      [sudo] password for anton:
      root@postgres4:~#
      root@postgres4:~# echo "listen_addresses = '*'" > /etc/postgresql/16/replication/conf.d/hw_10.conf
      root@postgres4:~#
      root@postgres4:~#
      logout
      anton@postgres4:~$
      logout
      Connection to postgres4.otus closed.
      [anton@manager ~]$
    ```
**Разрешаем подключения пользователей для репликации**
  * На ВМ 1 - для ВМ 2 и ВМ 3
    ```
      [anton@manager ~]$ ssh postgres1.otus
      anton@postgres1:~$ sudo su -
      [sudo] password for anton:
      root@postgres1:~#
      root@postgres1:~# echo 'host repl_db postgres2_repl postgres2.otus scram-sha-256' >> /etc/postgresql/14/replication/pg_hba.conf
      root@postgres1:~# echo 'host repl_db postgres3_repl postgres3.otus scram-sha-256' >> /etc/postgresql/14/replication/pg_hba.conf
      root@postgres1:~#
      root@postgres1:~#
      logout
      anton@postgres1:~$
      logout
      Connection to postgres1.otus closed.
      [anton@manager ~]$\
    ```
  * На ВМ 2 - для ВМ 1 и ВМ 3
    ```
      [anton@manager ~]$ ssh postgres2.otus
      anton@postgres2:~$ sudo su -
      [sudo] password for anton:
      root@postgres2:~#
      root@postgres2:~# echo 'host repl_db postgres1_repl postgres1.otus scram-sha-256' >> /etc/postgresql/15/replication/pg_hba.conf
      root@postgres2:~# echo 'host repl_db postgres3_repl postgres3.otus scram-sha-256' >> /etc/postgresql/15/replication/pg_hba.conf
      root@postgres2:~#
      root@postgres2:~#
      logout
      anton@postgres2:~$
      logout
      Connection to postgres2.otus closed.
      [anton@manager ~]$
    ```
  * На ВМ 3 - для ВМ 4
    ```
      [anton@manager ~]$ ssh postgres3.otus
      anton@postres3:~$ sudo su -
      [sudo] password for anton:
      root@postres3:~#
      root@postres3:~# echo 'host replication postgres4_repl postgres4.otus scram-sha-256' >> /etc/postgresql/16/replication/pg_hba.conf
      root@postres3:~#
      root@postres3:~#
      logout
      anton@postres3:~$
      logout
      Connection to postgres3.otus closed.
      [anton@manager ~]$
    ```
**Настраиваем уровень журнала предзаписи**
  * На ВМ 1
    ```
      [anton@manager ~]$ ssh postgres1.otus
      anton@postgres1:~$ sudo su -
      [sudo] password for anton:
      root@postgres1:~#
      root@postgres1:~# echo 'wal_level = logical' >> /etc/postgresql/14/replication/conf.d/hw_10.conf
      root@postgres1:~#
      root@postgres1:~#
      logout
      anton@postgres1:~$
      logout
      Connection to postgres1.otus closed.
      [anton@manager ~]$
    ```
  * На ВМ 2
    ```
      [anton@manager ~]$ ssh postgres2.otus
      anton@postgres2:~$ sudo su -
      [sudo] password for anton:
      root@postgres2:~#
      root@postgres2:~# echo 'wal_level = logical' >> /etc/postgresql/15/replication/conf.d/hw_10.conf
      root@postgres2:~#
      root@postgres2:~#
      logout
      anton@postgres2:~$
      logout
      Connection to postgres2.otus closed.
      [anton@manager ~]$
    ```
  * На ВМ 3
    ```
      [anton@manager ~]$ ssh postgres3.otus
      anton@postres3:~$ sudo su -
      [sudo] password for anton:
      root@postres3:~#
      root@postres3:~# echo 'wal_level = replica' >> /etc/postgresql/16/replication/conf.d/hw_10.conf
      root@postres3:~#
      root@postres3:~#
      logout
      anton@postres3:~$
      logout
      Connection to postgres3.otus closed.
      [anton@manager ~]$
    ```
**Для некоторого упрощения разрешаем подключения пользователю postgres с административной машины**
  * На ВМ 1
    ```
      [anton@manager ~]$ ssh postgres1.otus
      anton@postgres1:~$ sudo su -
      [sudo] password for anton:
      root@postgres1:~#
      root@postgres1:~# echo 'host all postgres manager.otus trust' >> /etc/postgresql/14/replication/pg_hba.conf
      root@postgres1:~#
      root@postgres1:~#
      logout
      anton@postgres1:~$
      logout
      Connection to postgres1.otus closed.
      [anton@manager ~]$
    ```
  * На ВМ 2
    ```
      [anton@manager ~]$ ssh postgres2.otus
      anton@postgres2:~$ sudo su -
      [sudo] password for anton:
      root@postgres2:~#
      root@postgres2:~# echo 'host all postgres manager.otus trust' >> /etc/postgresql/15/replication/pg_hba.conf
      root@postgres2:~#
      root@postgres2:~#
      logout
      anton@postgres2:~$
      logout
      Connection to postgres2.otus closed.
      [anton@manager ~]$
    ```
  * На ВМ 3
    ```
      [anton@manager ~]$ ssh postgres3.otus
      anton@postres3:~$ sudo su -
      [sudo] password for anton:
      root@postres3:~#
      root@postres3:~# echo 'host all postgres manager.otus trust' >> /etc/postgresql/16/replication/pg_hba.conf
      root@postres3:~#
      root@postres3:~#
      logout
      anton@postres3:~$
      logout
      Connection to postgres3.otus closed.
      [anton@manager ~]$
    ```
  * На ВМ 4
    ```
      [anton@manager ~]$ ssh postgres4.otus
      anton@postgres4:~$ sudo su -
      [sudo] password for anton:
      root@postgres4:~#
      root@postgres4:~# echo 'host all postgres manager.otus trust' >> /etc/postgresql/16/replication/pg_hba.conf
      root@postgres4:~#
      root@postgres4:~#
      logout
      anton@postgres4:~$
      logout
      Connection to postgres4.otus closed.
      [anton@manager ~]$
    ```
**Перезапускаем СУБД**
    ```
      [anton@manager ~]$ ssh postgres1.otus "sudo -S systemctl restart postgresql@14-replication"
      [sudo] password for anton:
      [anton@manager ~]$ ssh postgres2.otus "sudo -S systemctl restart postgresql@15-replication"
      [sudo] password for anton:
      [anton@manager ~]$ ssh postgres3.otus "sudo -S systemctl restart postgresql@16-replication"
      [sudo] password for anton:
      [anton@manager ~]$ ssh postgres4.otus "sudo -S systemctl restart postgresql@16-replication"
      [sudo] password for anton:
      [anton@manager ~]$
    ```

