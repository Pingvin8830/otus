**Описание/Пошаговая инструкция выполнения домашнего задания:**
* создайте виртуальную машину c Ubuntu 20.04/22.04 LTS в GCE/ЯО/Virtual Box/докере
  * Созданы ВМ otus_manager (10.0.2.4), otus_postgres_1 (10.0.2.5), otus_postgres_2 (10.0.2.6), otus_postgres_3 (10.0.2.7)
* поставьте на нее PostgreSQL 15 через sudo apt
  * Done
    ```
    anton@postgres1:~$ sudo apt install curl ca-certificates
    Чтение списков пакетов… Готово
    Построение дерева зависимостей… Готово
    Чтение информации о состоянии… Готово
    Уже установлен пакет ca-certificates самой новой версии (20230311ubuntu0.22.04.1).
    ca-certificates помечен как установленный вручную.
    Уже установлен пакет curl самой новой версии (7.81.0-1ubuntu1.16).
    curl помечен как установленный вручную.
    Обновлено 0 пакетов, установлено 0 новых пакетов, для удаления отмечено 0 пакетов, и 2 пакетов не обновлено.
    anton@postgres1:~$ sudo install -d /usr/share/postgresql-common/pgdg
    anton@postgres1:~$ sudo curl -o /usr/share/postgresql-common/pgdg/apt.postgresql.org.asc --fail https://www.postgresql.org/media/keys/ACCC4CF8.asc
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100  4812  100  4812    0     0   7218      0 --:--:-- --:--:-- --:--:--  7214
    anton@postgres1:~$ sudo sh -c 'echo "deb [signed-by=/usr/share/postgresql-common/pgdg/apt.postgresql.org.asc] https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
    anton@postgres1:~$ sudo apt update
    Сущ:1 http://ru.archive.ubuntu.com/ubuntu jammy InRelease
    Пол:2 http://ru.archive.ubuntu.com/ubuntu jammy-updates InRelease [128 kB]
    Сущ:3 http://ru.archive.ubuntu.com/ubuntu jammy-backports InRelease
    Сущ:4 http://security.ubuntu.com/ubuntu jammy-security InRelease
    Пол:5 https://apt.postgresql.org/pub/repos/apt jammy-pgdg InRelease [123 kB]
    Пол:6 https://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 Packages [337 kB]
    Получено 588 kB за 1с (608 kB/s)
    Чтение списков пакетов… Готово
    Построение дерева зависимостей… Готово
    Чтение информации о состоянии… Готово
    Может быть обновлено 2 пакета. Запустите «apt list --upgradable» для их показа.
    anton@postgres1:~$
    anton@postgres1:~$ sudo apt install postgresql-15                                                                                                                                                                          18:38:54 [75/1873]
    Чтение списков пакетов… Готово
    Построение дерева зависимостей… Готово
    Чтение информации о состоянии… Готово
    Будут установлены следующие дополнительные пакеты:
      libcommon-sense-perl libjson-perl libjson-xs-perl libllvm15 libpq5 libsensors-config libsensors5 libtypes-serialiser-perl postgresql-client-15 postgresql-client-common postgresql-common ssl-cert sysstat
    Предлагаемые пакеты:
      lm-sensors postgresql-doc-15 isag
    Следующие НОВЫЕ пакеты будут установлены:
      libcommon-sense-perl libjson-perl libjson-xs-perl libllvm15 libpq5 libsensors-config libsensors5 libtypes-serialiser-perl postgresql-15 postgresql-client-15 postgresql-client-common postgresql-common ssl-cert sysstat
    Обновлено 0 пакетов, установлено 14 новых пакетов, для удаления отмечено 0 пакетов, и 2 пакетов не обновлено.
    Необходимо скачать 45,4 MB архивов.
    После данной операции объём занятого дискового пространства возрастёт на 184 MB.
    Хотите продолжить? [Д/н]
    Пол:1 http://ru.archive.ubuntu.com/ubuntu jammy/main amd64 libjson-perl all 4.04000-1 [81,8 kB]
    Пол:2 http://ru.archive.ubuntu.com/ubuntu jammy/main amd64 ssl-cert all 1.1.2 [17,4 kB]
    Пол:3 http://ru.archive.ubuntu.com/ubuntu jammy/main amd64 libcommon-sense-perl amd64 3.75-2build1 [21,1 kB]
    Пол:4 http://ru.archive.ubuntu.com/ubuntu jammy/main amd64 libtypes-serialiser-perl all 1.01-1 [11,6 kB]
    Пол:5 http://ru.archive.ubuntu.com/ubuntu jammy/main amd64 libjson-xs-perl amd64 4.030-1build3 [87,2 kB]
    Пол:6 http://ru.archive.ubuntu.com/ubuntu jammy-updates/main amd64 libllvm15 amd64 1:15.0.7-0ubuntu0.22.04.3 [25,4 MB]
    Пол:7 https://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 postgresql-client-common all 260.pgdg22.04+1 [94,6 kB]
    Пол:8 https://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 postgresql-common all 260.pgdg22.04+1 [240 kB]
    Пол:9 https://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 libpq5 amd64 16.3-1.pgdg22.04+1 [217 kB]
    Пол:10 https://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 postgresql-client-15 amd64 15.7-1.pgdg22.04+1 [1 688 kB]
    Пол:11 http://ru.archive.ubuntu.com/ubuntu jammy/main amd64 libsensors-config all 1:3.6.0-7ubuntu1 [5 274 B]
    Пол:12 http://ru.archive.ubuntu.com/ubuntu jammy/main amd64 libsensors5 amd64 1:3.6.0-7ubuntu1 [26,3 kB]
    Пол:13 http://ru.archive.ubuntu.com/ubuntu jammy-updates/main amd64 sysstat amd64 12.5.2-2ubuntu0.2 [487 kB]
    Пол:14 https://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 postgresql-15 amd64 15.7-1.pgdg22.04+1 [17,1 MB]
    Получено 45,4 MB за 8с (5 709 kB/s)
    Предварительная настройка пакетов …
    Выбор ранее не выбранного пакета libjson-perl.
    (Чтение базы данных … на данный момент установлено 74662 файла и каталога.)
    Подготовка к распаковке …/00-libjson-perl_4.04000-1_all.deb …
    Распаковывается libjson-perl (4.04000-1) …
    Выбор ранее не выбранного пакета postgresql-client-common.
    Подготовка к распаковке …/01-postgresql-client-common_260.pgdg22.04+1_all.deb …
    Распаковывается postgresql-client-common (260.pgdg22.04+1) …
    Выбор ранее не выбранного пакета ssl-cert.
    Подготовка к распаковке …/02-ssl-cert_1.1.2_all.deb …
    Распаковывается ssl-cert (1.1.2) …
    Выбор ранее не выбранного пакета postgresql-common.
    Подготовка к распаковке …/03-postgresql-common_260.pgdg22.04+1_all.deb …
    Добавляется «отклонение /usr/bin/pg_config в /usr/bin/pg_config.libpq-dev из-за postgresql-common»
    Распаковывается postgresql-common (260.pgdg22.04+1) …
    Выбор ранее не выбранного пакета libcommon-sense-perl:amd64.
    Подготовка к распаковке …/04-libcommon-sense-perl_3.75-2build1_amd64.deb …
    Распаковывается libcommon-sense-perl:amd64 (3.75-2build1) …
    Выбор ранее не выбранного пакета libtypes-serialiser-perl.
    Подготовка к распаковке …/05-libtypes-serialiser-perl_1.01-1_all.deb …
    Распаковывается libtypes-serialiser-perl (1.01-1) …
    Выбор ранее не выбранного пакета libjson-xs-perl.
    Подготовка к распаковке …/06-libjson-xs-perl_4.030-1build3_amd64.deb …
    Распаковывается libjson-xs-perl (4.030-1build3) …
    Выбор ранее не выбранного пакета libllvm15:amd64.
    Подготовка к распаковке …/07-libllvm15_1%3a15.0.7-0ubuntu0.22.04.3_amd64.deb …
    Распаковывается libllvm15:amd64 (1:15.0.7-0ubuntu0.22.04.3) …
    Выбор ранее не выбранного пакета libpq5:amd64.
    Подготовка к распаковке …/08-libpq5_16.3-1.pgdg22.04+1_amd64.deb …
    Распаковывается libpq5:amd64 (16.3-1.pgdg22.04+1) …
    Выбор ранее не выбранного пакета libsensors-config.
    Подготовка к распаковке …/09-libsensors-config_1%3a3.6.0-7ubuntu1_all.deb …
    Распаковывается libsensors-config (1:3.6.0-7ubuntu1) …
    Выбор ранее не выбранного пакета libsensors5:amd64.
    Подготовка к распаковке …/10-libsensors5_1%3a3.6.0-7ubuntu1_amd64.deb …
    Распаковывается libsensors5:amd64 (1:3.6.0-7ubuntu1) …
    Выбор ранее не выбранного пакета postgresql-client-15.
    Подготовка к распаковке …/11-postgresql-client-15_15.7-1.pgdg22.04+1_amd64.deb …
    Распаковывается postgresql-client-15 (15.7-1.pgdg22.04+1) …
    Выбор ранее не выбранного пакета postgresql-15.
    Подготовка к распаковке …/12-postgresql-15_15.7-1.pgdg22.04+1_amd64.deb …
    Распаковывается postgresql-15 (15.7-1.pgdg22.04+1) …
    Выбор ранее не выбранного пакета sysstat.
    Подготовка к распаковке …/13-sysstat_12.5.2-2ubuntu0.2_amd64.deb …
    Распаковывается sysstat (12.5.2-2ubuntu0.2) …
    Настраивается пакет postgresql-client-common (260.pgdg22.04+1) …
    Настраивается пакет libsensors-config (1:3.6.0-7ubuntu1) …
    Настраивается пакет libpq5:amd64 (16.3-1.pgdg22.04+1) …
    Настраивается пакет libcommon-sense-perl:amd64 (3.75-2build1) …
    Настраивается пакет postgresql-client-15 (15.7-1.pgdg22.04+1) …
    update-alternatives: используется /usr/share/postgresql/15/man/man1/psql.1.gz для предоставления /usr/share/man/man1/psql.1.gz (psql.1.gz) в автоматическом режиме
    Настраивается пакет ssl-cert (1.1.2) …
    Настраивается пакет libsensors5:amd64 (1:3.6.0-7ubuntu1) …
    Настраивается пакет libtypes-serialiser-perl (1.01-1) …
    Настраивается пакет libllvm15:amd64 (1:15.0.7-0ubuntu0.22.04.3) …
    Настраивается пакет libjson-perl (4.04000-1) …
    Настраивается пакет sysstat (12.5.2-2ubuntu0.2) …

    Creating config file /etc/default/sysstat with new version
    update-alternatives: используется /usr/bin/sar.sysstat для предоставления /usr/bin/sar (sar) в автоматическом режиме
    Created symlink /etc/systemd/system/sysstat.service.wants/sysstat-collect.timer → /lib/systemd/system/sysstat-collect.timer.
    Created symlink /etc/systemd/system/sysstat.service.wants/sysstat-summary.timer → /lib/systemd/system/sysstat-summary.timer.
    Created symlink /etc/systemd/system/multi-user.target.wants/sysstat.service → /lib/systemd/system/sysstat.service.
    Настраивается пакет libjson-xs-perl (4.030-1build3) …
    Настраивается пакет postgresql-common (260.pgdg22.04+1) …
    Adding user postgres to group ssl-cert

    Creating config file /etc/postgresql-common/createcluster.conf with new version
    Building PostgreSQL dictionaries from installed myspell/hunspell packages...
    Removing obsolete dictionary files:
    Created symlink /etc/systemd/system/multi-user.target.wants/postgresql.service → /lib/systemd/system/postgresql.service.
    Настраивается пакет postgresql-15 (15.7-1.pgdg22.04+1) …
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
    Обрабатываются триггеры для man-db (2.10.2-1) …
    Обрабатываются триггеры для libc-bin (2.35-0ubuntu3.8) …
    Scanning processes...
    Scanning candidates...
    Scanning linux images...

    Running kernel seems to be up-to-date.

    Restarting services...
    Service restarts being deferred:
     systemctl restart unattended-upgrades.service

    No containers need to be restarted.

    No user sessions are running outdated binaries.

    No VM guests are running outdated hypervisor (qemu) binaries on this host.
    anton@postgres1:~$
    ```
проверьте что кластер запущен через sudo -u postgres pg_lsclusters
зайдите из под пользователя postgres в psql и сделайте произвольную таблицу с произвольным содержимым
postgres=# create table test(c1 text);
postgres=# insert into test values('1');
\q
остановите postgres например через sudo -u postgres pg_ctlcluster 15 main stop
создайте новый диск к ВМ размером 10GB
добавьте свеже-созданный диск к виртуальной машине - надо зайти в режим ее редактирования и дальше выбрать пункт attach existing disk
проинициализируйте диск согласно инструкции и подмонтировать файловую систему, только не забывайте менять имя диска на актуальное, в вашем случае это скорее всего будет /dev/sdb - https://www.digitalocean.com/community/tutorials/how-to-partition-and-format-storage-devices-in-linux
перезагрузите инстанс и убедитесь, что диск остается примонтированным (если не так смотрим в сторону fstab)
сделайте пользователя postgres владельцем /mnt/data - chown -R postgres:postgres /mnt/data/
перенесите содержимое /var/lib/postgres/15 в /mnt/data - mv /var/lib/postgresql/15/mnt/data
попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 15 main start
напишите получилось или нет и почему
задание: найти конфигурационный параметр в файлах раположенных в /etc/postgresql/15/main который надо поменять и поменяйте его
напишите что и почему поменяли
попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 15 main start
напишите получилось или нет и почему
зайдите через через psql и проверьте содержимое ранее созданной таблицы
задание со звездочкой *: не удаляя существующий инстанс ВМ сделайте новый, поставьте на его PostgreSQL, удалите файлы с данными из /var/lib/postgres, перемонтируйте внешний диск который сделали ранее от первой виртуальной машины ко второй и запустите PostgreSQL на второй машине так чтобы он работал с данными на внешнем диске, расскажите как вы это сделали и что в итоге получилось.

Критерии оценки:
Выполнение ДЗ: 10 баллов
плюс 5 баллов за задание со *
плюс 2 балла за красивое решение
минус 2 балла за рабочее решение, и недостатки указанные преподавателем не устранены
