**Описание/Пошаговая инструкция выполнения домашнего задания:**
* создайте виртуальную машину c Ubuntu 20.04/22.04 LTS в GCE/ЯО/Virtual Box/докере
  * Создана ВМ otus_manager (10.0.2.4), на которой настроен DNS сервер
    ```
    [anton@manager ~]$ nslookup manager.otus
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    Non-authoritative answer:
    Name:   manager.otus
    Address: 10.0.2.4

    [anton@manager ~]$ nslookup postgres1.otus
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    Non-authoritative answer:
    Name:   postgres1.otus
    Address: 10.0.2.5

    [anton@manager ~]$ nslookup postgres2.otus
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    Non-authoritative answer:
    Name:   postgres2.otus
    Address: 10.0.2.6

    [anton@manager ~]$ nslookup postgres3.otus
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    Non-authoritative answer:
    Name:   postgres3.otus
    Address: 10.0.2.7

    [anton@manager ~]$ ip a | grep 10.
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
        inet 10.0.2.4/24 brd 10.0.2.255 scope global enp0s3
    3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    [anton@manager ~]$
    ```

  * Созданы ВМ otus_postgres_1 (10.0.2.5), otus_postgres_2 (10.0.2.6), otus_postgres_3 (10.0.2.7)

  ![Список ВМ](https://github.com/Pingvin8830/otus/blob/main/HW_03%20Installation%20PostgreSQL%20on%20hosts/VMs.PNG)
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
* проверьте что кластер запущен через sudo -u postgres pg_lsclusters
  * Done
    ```
    anton@postgres1:~$ sudo -u postgres pg_lsclusters
    Ver Cluster Port Status Owner    Data directory              Log file
    15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
    anton@postgres1:~$
    ```
* зайдите из под пользователя postgres в psql и сделайте произвольную таблицу с произвольным содержимым
  * Done
    ```
    anton@postgres1:~$ sudo su - postgres -c psql
    psql (15.7 (Ubuntu 15.7-1.pgdg22.04+1))
    Введите "help", чтобы получить справку.

    postgres=# CREATE TABLE control_table (
    postgres(#   id INT,
    postgres(#   dt TIMESTAMP
    postgres(# );
    CREATE TABLE
    postgres=# INSERT INTO control_table (id ,dt) VALUES (1, now());
    INSERT 0 1
    postgres=# SELECT * FROM control_table;
     id |             dt
    ----+----------------------------
      1 | 2024-06-24 15:47:03.208952
    (1 строка)

    postgres=#
    ```
* остановите postgres например через sudo -u postgres pg_ctlcluster 15 main stop
  * Done
    ```
    anton@postgres1:~$ sudo systemctl stop postgresql@15-main
    [sudo] password for anton:
    anton@postgres1:~$ sudo -u postgres pg_lsclusters
    Ver Cluster Port Status Owner    Data directory              Log file
    15  main    5432 down   postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
    anton@postgres1:~$
    ```
* создайте новый диск к ВМ размером 10GB
  * Текущая конфигурация:
    ```
    anton@postgres1:~$ lsblk
    NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
    loop0                       7:0    0 63,9M  1 loop /snap/core20/2105
    loop1                       7:1    0   87M  1 loop /snap/lxd/27037
    loop2                       7:2    0 40,4M  1 loop /snap/snapd/20671
    sda                         8:0    0   10G  0 disk
    ├─sda1                      8:1    0  538M  0 part /boot/efi
    ├─sda2                      8:2    0  1,8G  0 part /boot
    └─sda3                      8:3    0  7,7G  0 part
      └─ubuntu--vg-ubuntu--lv 253:0    0  7,7G  0 lvm  /
    sr0                        11:0    1 1024M  0 rom
    anton@postgres1:~$
    ```
    ![postgres_1_one_disk](https://github.com/Pingvin8830/otus/blob/main/HW_03%20Installation%20PostgreSQL%20on%20hosts/postgres_1_disks_1.PNG)

  * Добавим диск "физически"

  ![postgres_1_two_disks](https://github.com/Pingvin8830/otus/blob/main/HW_03%20Installation%20PostgreSQL%20on%20hosts/postgres_1_disks_extend.PNG)
* добавьте свеже-созданный диск к виртуальной машине - надо зайти в режим ее редактирования и дальше выбрать пункт attach existing disk
  * Новая конфигурация
    ```
    anton@postgres1:~$ lsblk
    NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
    loop0                       7:0    0 63,9M  1 loop /snap/core20/2105
    loop1                       7:1    0   87M  1 loop /snap/lxd/27037
    loop2                       7:2    0 40,4M  1 loop /snap/snapd/20671
    sda                         8:0    0   10G  0 disk
    ├─sda1                      8:1    0  538M  0 part /boot/efi
    ├─sda2                      8:2    0  1,8G  0 part /boot
    └─sda3                      8:3    0  7,7G  0 part
      └─ubuntu--vg-ubuntu--lv 253:0    0  7,7G  0 lvm  /
    sdb                         8:16   0   10G  0 disk
    sr0                        11:0    1 1024M  0 rom
    anton@postgres1:~$
    ```
* проинициализируйте диск согласно инструкции и подмонтировать файловую систему
  * Done
    ```
    anton@postgres1:~$ sudo fdisk /dev/sdb
    [sudo] password for anton:

    Welcome to fdisk (util-linux 2.37.2).
    Changes will remain in memory only, until you decide to write them.
    Be careful before using the write command.

    Device does not contain a recognized partition table.
    Created a new DOS disklabel with disk identifier 0x41a77d91.

    Command (m for help): g
    Created a new GPT disklabel (GUID: 6AF9FF8D-1A6C-2846-BB97-2D558B6B71DB).

    Command (m for help): n
    Partition number (1-128, default 1):
    First sector (2048-20971486, default 2048):
    Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-20971486, default 20971486):

    Created a new partition 1 of type 'Linux filesystem' and of size 10 GiB.

    Command (m for help): w
    The partition table has been altered.
    Calling ioctl() to re-read partition table.
    Syncing disks.

    anton@postgres1:~$ mkfs.ext4 /dev/sdb1
    mke2fs 1.46.5 (30-Dec-2021)
    mkfs.ext4: Permission denied while trying to determine filesystem size
    anton@postgres1:~$ sudo mkfs.ext4 /dev/sdb1
    mke2fs 1.46.5 (30-Dec-2021)
    Creating filesystem with 2621179 4k blocks and 655360 inodes
    Filesystem UUID: 52480aa0-6146-4b05-9a87-590407079804
    Superblock backups stored on blocks:
            32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

    Allocating group tables: done
    Writing inode tables: done
    Creating journal (16384 blocks): done
    Writing superblocks and filesystem accounting information: done

    anton@postgres1:~$ sudo mount /dev/sdb1 /mnt
    anton@postgres1:~$ sudo su -
    root@postgres1:~# echo "UUID=$(blkid /dev/sdb1 | cut -d'=' -f2 | cut -d' ' -f1 | sed 's/\"//g')       /mnt            ext4            rw,relatime     0 1" >> /etc/fstab
    root@postgres1:~# cat /etc/fstab
    # /etc/fstab: static file system information.
    #
    # Use 'blkid' to print the universally unique identifier for a
    # device; this may be used with UUID= as a more robust way to name devices
    # that works even if disks are added and removed. See fstab(5).
    #
    # <file system> <mount point>   <type>  <options>       <dump>  <pass>
    # / was on /dev/ubuntu-vg/ubuntu-lv during curtin installation
    /dev/disk/by-id/dm-uuid-LVM-KOQOy7XvH81aBg1MBpsyKGZl9v0jfVoikrtaq6HPopckXQz08iy6waDdGEdJ9KAQ / ext4 defaults 0 1
    # /boot was on /dev/sda2 during curtin installation
    /dev/disk/by-uuid/83db8924-4e5e-4107-864b-348b385456fe /boot ext4 defaults 0 1
    # /boot/efi was on /dev/sda1 during curtin installation
    /dev/disk/by-uuid/D59E-DA34 /boot/efi vfat defaults 0 1
    # /etc/fstab: static file system information.
    #
    # Use 'blkid' to print the universally unique identifier for a
    # device; this may be used with UUID= as a more robust way to name devices
    # that works even if disks are added and removed. See fstab(5).
    #
    # <file system> <mount point>   <type>  <options>       <dump>  <pass>
    # /dev/sdb1
    UUID=52480aa0-6146-4b05-9a87-590407079804       /mnt            ext4            rw,relatime     0 1

    root@postgres1:~#
    ```
* перезагрузите инстанс и убедитесь, что диск остается примонтированным (если не так смотрим в сторону fstab)
  * Done
    ```
    root@postgres1:~# lsblk
    NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
    loop0                       7:0    0 63,9M  1 loop /snap/core20/2105
    loop1                       7:1    0   87M  1 loop /snap/lxd/27037
    loop2                       7:2    0 40,4M  1 loop /snap/snapd/20671
    sda                         8:0    0   10G  0 disk
    ├─sda1                      8:1    0  538M  0 part /boot/efi
    ├─sda2                      8:2    0  1,8G  0 part /boot
    └─sda3                      8:3    0  7,7G  0 part
      └─ubuntu--vg-ubuntu--lv 253:0    0  7,7G  0 lvm  /
    sdb                         8:16   0   10G  0 disk
    └─sdb1                      8:17   0   10G  0 part /mnt
    sr0                        11:0    1 1024M  0 rom
    root@postgres1:~# reboot
    Connection to postgres1.otus closed by remote host.
    Connection to postgres1.otus closed.
    [anton@manager ~]$ ssh postgres1.otus
    anton@postgres1.otus's password:
    Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-112-generic x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/pro

     System information as of Пн 24 июн 2024 16:56:10 UTC

      System load:  0.0               Processes:               151
      Usage of /:   43.0% of 7.50GB   Users logged in:         0
      Memory usage: 6%                IPv4 address for enp0s3: 10.0.2.5
      Swap usage:   0%


    Расширенное поддержание безопасности (ESM) для Applications выключено.

    0 обновлений может быть применено немедленно.

    Включите ESM Apps для получения дополнительных будущих обновлений безопасности.
    Смотрите https://ubuntu.com/esm или выполните: sudo pro status


    Last login: Mon Jun 24 16:46:03 2024 from 10.0.2.4
    anton@postgres1:~$ lsblk
    NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
    loop0                       7:0    0 63,9M  1 loop /snap/core20/2105
    loop1                       7:1    0   87M  1 loop /snap/lxd/27037
    loop2                       7:2    0 40,4M  1 loop /snap/snapd/20671
    sda                         8:0    0   10G  0 disk
    ├─sda1                      8:1    0  538M  0 part /boot/efi
    ├─sda2                      8:2    0  1,8G  0 part /boot
    └─sda3                      8:3    0  7,7G  0 part
      └─ubuntu--vg-ubuntu--lv 253:0    0  7,7G  0 lvm  /
    sdb                         8:16   0   10G  0 disk
    └─sdb1                      8:17   0   10G  0 part /mnt
    sr0                        11:0    1 1024M  0 rom
    anton@postgres1:~$
    ```
* сделайте пользователя postgres владельцем /mnt/data - chown -R postgres:postgres /mnt/data/
  * Done
    ```
    anton@postgres1:~$ sudo mkdir -v /mnt/data
    [sudo] password for anton:
    mkdir: created directory '/mnt/data'
    anton@postgres1:~$ sudo chown -Rv postgres:postgres /mnt/data
    ownership of '/mnt/data' retained as postgres:postgres
    anton@postgres1:~$
    ```
* перенесите содержимое /var/lib/postgres/15 в /mnt/data - mv /var/lib/postgresql/15 /mnt/data
  * Done
    ```
    anton@postgres1:~$ sudo pg_lsclusters
    Ver Cluster Port Status Owner    Data directory              Log file
    15  main    5432 online postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
    anton@postgres1:~$ sudo systemctl stop postgresql@15-main
    anton@postgres1:~$ sudo pg_lsclusters
    Ver Cluster Port Status Owner    Data directory              Log file
    15  main    5432 down   postgres /var/lib/postgresql/15/main /var/log/postgresql/postgresql-15-main.log
    anton@postgres1:~$ sudo mv /var/lib/postgresql/15 /mnt/data/
    anton@postgres1:~$
    ```
* попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 15 main start
  * Fail
    ```
    anton@postgres1:~$ sudo systemctl start postgresql@15-main
    Job for postgresql@15-main.service failed because the service did not take the steps required by its unit configuration.
    See "systemctl status postgresql@15-main.service" and "journalctl -xeu postgresql@15-main.service" for details.
    anton@postgres1:~$
    ```
* напишите получилось или нет и почему
  * Нет, не получилось. Потому что PGDATA не существует в расположениии, которое ожидает СУБД
    ```
    anton@postgres1:~$ systemctl status postgresql@15-main
    × postgresql@15-main.service - PostgreSQL Cluster 15-main
         Loaded: loaded (/lib/systemd/system/postgresql@.service; enabled-runtime; vendor preset: enabled)
         Active: failed (Result: protocol) since Mon 2024-06-24 17:04:52 UTC; 1h 54min ago
        Process: 1256 ExecStart=/usr/bin/pg_ctlcluster --skip-systemctl-redirect 15-main start (code=exited, status=1/FAILURE)
            CPU: 30ms

    июн 24 17:04:52 postgres1 systemd[1]: Starting PostgreSQL Cluster 15-main...
    июн 24 17:04:52 postgres1 postgresql@15-main[1256]: Error: /var/lib/postgresql/15/main is not accessible or does not exist
    июн 24 17:04:52 postgres1 systemd[1]: postgresql@15-main.service: Can't open PID file /run/postgresql/15-main.pid (yet?) after start: Operation not permitted
    июн 24 17:04:52 postgres1 systemd[1]: postgresql@15-main.service: Failed with result 'protocol'.
    июн 24 17:04:52 postgres1 systemd[1]: Failed to start PostgreSQL Cluster 15-main.
    anton@postgres1:~$
    ```
* задание: найти конфигурационный параметр в файлах раположенных в /etc/postgresql/15/main который надо поменять и поменяйте его
  * Done
    ```
    anton@postgres1:~$ sudo grep -R '/var/lib/postgresql/15/main' /etc/postgresql/15/main/*
    /etc/postgresql/15/main/postgresql.conf:data_directory = '/var/lib/postgresql/15/main'          # use data in another directory
    anton@postgres1:~$ sudo cp -v /etc/postgresql/15/main/postgresql.conf /etc/postgresql/15/main/postgresql.conf.def
    '/etc/postgresql/15/main/postgresql.conf' -> '/etc/postgresql/15/main/postgresql.conf.def'
    anton@postgres1:~$ sudo vim /etc/postgresql/15/main/postgresql.conf
    anton@postgres1:~$ grep data_directory /etc/postgresql/15/main/postgresql.conf
    #data_directory = '/var/lib/postgresql/15/main'         # use data in another directory
    data_directory = '/mnt/data/15/main'            # use data in another directory
    anton@postgres1:~$
    ```
* напишите что и почему поменяли
  * Заменили параметр конфигурации data_directory. Это каталог, в котором ожидаются файлы кластера данных PostgreSQL. Если каталог пуст, не существует, имеет неверные данные и т.п. - СУБД не запустится
* попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 15 main start
  * Done
    ```
    anton@postgres1:~$ sudo systemctl start postgresql@15-main
    anton@postgres1:~$ sudo systemctl status postgresql@15-main
    ● postgresql@15-main.service - PostgreSQL Cluster 15-main
         Loaded: loaded (/lib/systemd/system/postgresql@.service; enabled-runtime; vendor preset: enabled)
         Active: active (running) since Mon 2024-06-24 19:09:16 UTC; 3s ago
        Process: 1629 ExecStart=/usr/bin/pg_ctlcluster --skip-systemctl-redirect 15-main start (code=exited, status=0/SUCCESS)
       Main PID: 1634 (postgres)
          Tasks: 6 (limit: 4537)
         Memory: 18.7M
            CPU: 114ms
         CGroup: /system.slice/system-postgresql.slice/postgresql@15-main.service
                 ├─1634 /usr/lib/postgresql/15/bin/postgres -D /mnt/data/15/main -c config_file=/etc/postgresql/15/main/postgresql.conf
                 ├─1635 "postgres: 15/main: checkpointer " "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" ">
                 ├─1636 "postgres: 15/main: background writer " "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "">
                 ├─1638 "postgres: 15/main: walwriter " "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" ">
                 ├─1639 "postgres: 15/main: autovacuum launcher " "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" >
                 └─1640 "postgres: 15/main: logical replication launcher " "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" "" >

    июн 24 19:09:14 postgres1 systemd[1]: Starting PostgreSQL Cluster 15-main...
    июн 24 19:09:16 postgres1 systemd[1]: Started PostgreSQL Cluster 15-main.
    lines 1-18/18 (END)
    anton@postgres1:~$ 
    ```
* напишите получилось или нет и почему
  * Получилось. Потому что теперь data_directory указывает на правильное расположение данных кластера и права на файлы (поскольку мы их не меняли) - правильные
* зайдите через через psql и проверьте содержимое ранее созданной таблицы
  * Done
    ```
    anton@postgres1:~$ sudo su - postgres -c psql
    psql (15.7 (Ubuntu 15.7-1.pgdg22.04+1))
    Введите "help", чтобы получить справку.

    postgres=# select * from control_table;
     id |             dt
    ----+----------------------------
      1 | 2024-06-24 15:47:03.208952
    (1 строка)

    postgres=#
    ```
* задание со звездочкой *: не удаляя существующий инстанс ВМ сделайте новый, поставьте на его PostgreSQL, удалите файлы с данными из /var/lib/postgres, перемонтируйте внешний диск который сделали ранее от первой виртуальной машины ко второй и запустите PostgreSQL на второй машине так чтобы он работал с данными на внешнем диске, расскажите как вы это сделали и что в итоге получилось.
  * Проверяем UUID файловой системы, где сейчас находится PGDATA
    ```
    anton@postgres1:~$ lsblk -f /dev/sdb1
    NAME FSTYPE FSVER LABEL UUID                                 FSAVAIL FSUSE% MOUNTPOINTS
    sdb1 ext4   1.0         52480aa0-6146-4b05-9a87-590407079804    9,2G     0% /mnt
    anton@postgres1:~$
    ```
  * Останавливаем ВМ postgres_1
    ```
    anton@postgres1:~$ sudo poweroff
    Connection to postgres1.otus closed by remote host.
    Connection to postgres1.otus closed.
    [anton@manager ~]$
    ```
  * Проверяем диски на postgres_2
    ```
    anton@postgres2:~$ lsblk
    NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
    loop0                       7:0    0 63,9M  1 loop /snap/core20/2105
    loop1                       7:1    0 63,9M  1 loop /snap/core20/2318
    loop2                       7:2    0   87M  1 loop /snap/lxd/27037
    loop3                       7:3    0   87M  1 loop /snap/lxd/28373
    loop4                       7:4    0 40,4M  1 loop /snap/snapd/20671
    loop5                       7:5    0 38,8M  1 loop /snap/snapd/21759
    sda                         8:0    0   10G  0 disk
    ├─sda1                      8:1    0  538M  0 part /boot/efi
    ├─sda2                      8:2    0  1,8G  0 part /boot
    └─sda3                      8:3    0  7,7G  0 part
      └─ubuntu--vg-ubuntu--lv 253:0    0  7,7G  0 lvm  /
    sr0                        11:0    1 1024M  0 rom
    anton@postgres2:~$
    ```
    ![postgres_2_disks_one](https://github.com/Pingvin8830/otus/blob/hw_03/HW_03%20Installation%20PostgreSQL%20on%20hosts/postgres_2_disks_one.PNG)
  * Останавливаем ВМ postgres_2
    ```
    [anton@manager ~]$ ssh postgres2.otus
    anton@postgres2.otus's password:
    Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-112-generic x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/pro

      System information as of Пн 24 июн 2024 19:16:19 UTC

      System load:  0.1728515625      Processes:               151
      Usage of /:   41.9% of 7.50GB   Users logged in:         0
      Memory usage: 6%                IPv4 address for enp0s3: 10.0.2.6
      Swap usage:   0%


    Расширенное поддержание безопасности (ESM) для Applications выключено.

    25 обновлений может быть применено немедленно.
    Чтобы просмотреть дополнительные обновления выполните: apt list --upgradable

    Включите ESM Apps для получения дополнительных будущих обновлений безопасности.
    Смотрите https://ubuntu.com/esm или выполните: sudo pro status


    Last login: Mon Jun 24 19:16:19 2024 from 10.0.2.4
    anton@postgres2:~$ sudo poweroff
    [sudo] password for anton:
    Connection to postgres2.otus closed by remote host.
    Connection to postgres2.otus closed.
    [anton@manager ~]$
    ```

  * Физически отключаем диск от postgres_1
    ```
    ```
  ![postgres_1_disks_one_again](https://github.com/Pingvin8830/otus/blob/main/HW_03%20Installation%20PostgreSQL%20on%20hosts/postgres_1_disks_one_more.PNG)

  * Физически подключем тот же диск к postgres_2
    ```
    ```
  ![postgres_2_disks_two](https://github.com/Pingvin8830/otus/blob/main/HW_03%20Installation%20PostgreSQL%20on%20hosts/postgres_2_disks_two.PNG)

  * Запускаем postgres_2, убеждаемся, что диск подключен и UUID файловой системы тот же
    ```
    [anton@manager ~]$ ssh postgres2.otus
    anton@postgres2.otus's password:
    Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-112-generic x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/pro

      System information as of Пн 24 июн 2024 19:22:07 UTC
    
      System load:  0.39013671875     Processes:               141
      Usage of /:   42.2% of 7.50GB   Users logged in:         0
      Memory usage: 5%                IPv4 address for enp0s3: 10.0.2.6
      Swap usage:   0%


    Расширенное поддержание безопасности (ESM) для Applications выключено.

    25 обновлений может быть применено немедленно.
    Чтобы просмотреть дополнительные обновления выполните: apt list --upgradable
    
    Включите ESM Apps для получения дополнительных будущих обновлений безопасности.
    Смотрите https://ubuntu.com/esm или выполните: sudo pro status


    Last login: Mon Jun 24 19:16:23 2024 from 10.0.2.4
    anton@postgres2:~$ lsblk -f
    NAME                      FSTYPE      FSVER    LABEL UUID                                   FSAVAIL FSUSE% MOUNTPOINTS
    loop0                     squashfs    4.0                                                         0   100% /snap/core20/2105
    loop1                     squashfs    4.0                                                         0   100% /snap/core20/2318
    loop2                     squashfs    4.0                                                         0   100% /snap/lxd/27037
    loop3                     squashfs    4.0                                                         0   100% /snap/lxd/28373
    loop4                     squashfs    4.0                                                         0   100% /snap/snapd/20671
    loop5                     squashfs    4.0                                                         0   100% /snap/snapd/21759
    sda
    ├─sda1                    vfat        FAT32          DF28-3DF7                               530,8M     1% /boot/efi
    ├─sda2                    ext4        1.0            095cfad6-fd60-4208-9c1a-0a4cd0799b74      1,5G     8% /boot
    └─sda3                    LVM2_member LVM2 001       BfODnq-wuCv-FZdj-h8Ih-zM4f-hHru-xGT6dD
      └─ubuntu--vg-ubuntu--lv ext4        1.0            a6f56adb-e3be-420b-bcda-791aef3a3e4a      3,9G    42% /
    sdb
    └─sdb1                    ext4        1.0            52480aa0-6146-4b05-9a87-590407079804
    sr0
    anton@postgres2:~$
    ```
  * Настраиваем автомонтирование доп диска
    ```
    anton@postgres2:~$ sudo su -
    [sudo] password for anton:
    root@postgres2:~# echo "UUID=$(blkid /dev/sdb1 | cut -d'=' -f2 | cut -d' ' -f1 | sed 's/\"//g')       /mnt            ext4            rw,relatime     0 1" >> /etc/fstab
    root@postgres2:~# cat /etc/fstab
    # /etc/fstab: static file system information.
    #
    # Use 'blkid' to print the universally unique identifier for a
    # device; this may be used with UUID= as a more robust way to name devices
    # that works even if disks are added and removed. See fstab(5).
    #
    # <file system> <mount point>   <type>  <options>       <dump>  <pass>
    # / was on /dev/ubuntu-vg/ubuntu-lv during curtin installation
    /dev/disk/by-id/dm-uuid-LVM-AadFMeRD6cCVG1UE6ir2QIHopi6hAhXrOhtCbExi74dRta9SbkrzgMVtVfSDa0A4 / ext4 defaults 0 1
    # /boot was on /dev/sda2 during curtin installation
    /dev/disk/by-uuid/095cfad6-fd60-4208-9c1a-0a4cd0799b74 /boot ext4 defaults 0 1
    # /boot/efi was on /dev/sda1 during curtin installation
    /dev/disk/by-uuid/DF28-3DF7 /boot/efi vfat defaults 0 1
    UUID=52480aa0-6146-4b05-9a87-590407079804       /mnt            ext4            rw,relatime     0 1
    root@postgres2:~#
    ```
  * Устанавливаем PostgreSQL
    ```
    root@postgres2:~# apt install curl ca-certificates
    Чтение списков пакетов… Готово
    Построение дерева зависимостей… Готово
    Чтение информации о состоянии… Готово
    Уже установлен пакет ca-certificates самой новой версии (20230311ubuntu0.22.04.1).
    ca-certificates помечен как установленный вручную.
    Уже установлен пакет curl самой новой версии (7.81.0-1ubuntu1.16).
    curl помечен как установленный вручную.
    Обновлено 0 пакетов, установлено 0 новых пакетов, для удаления отмечено 0 пакетов, и 26 пакетов не обновлено.
    root@postgres2:~# install -d /usr/share/postgresql-common/pgdg
    root@postgres2:~# curl -o /usr/share/postgresql-common/pgdg/apt.postgresql.org.asc --fail https://www.postgresql.org/media/keys/ACCC4CF8.asc
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100  4812  100  4812    0     0   4659      0  0:00:01  0:00:01 --:--:--  4662
    root@postgres2:~# sh -c 'echo "deb [signed-by=/usr/share/postgresql-common/pgdg/apt.postgresql.org.asc] https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
    root@postgres2:~# apt update
    Сущ:1 http://ru.archive.ubuntu.com/ubuntu jammy InRelease
    Пол:2 http://ru.archive.ubuntu.com/ubuntu jammy-updates InRelease [128 kB]
    Пол:3 http://security.ubuntu.com/ubuntu jammy-security InRelease [129 kB]
    Сущ:4 http://ru.archive.ubuntu.com/ubuntu jammy-backports InRelease
    Пол:5 http://ru.archive.ubuntu.com/ubuntu jammy/main Translation-ru [344 kB]
    Пол:6 http://ru.archive.ubuntu.com/ubuntu jammy/restricted Translation-ru [896 B]
    Пол:7 http://ru.archive.ubuntu.com/ubuntu jammy/universe Translation-ru [1 369 kB]
    Пол:8 http://ru.archive.ubuntu.com/ubuntu jammy/multiverse Translation-ru [68,5 kB]
    Пол:9 https://apt.postgresql.org/pub/repos/apt jammy-pgdg InRelease [123 kB]
    Пол:10 https://apt.postgresql.org/pub/repos/apt jammy-pgdg/main amd64 Packages [337 kB]
    Получено 2 499 kB за 1с (2 077 kB/s)
    Чтение списков пакетов… Готово
    Построение дерева зависимостей… Готово
    Чтение информации о состоянии… Готово
    Может быть обновлено 26 пакетов. Запустите «apt list --upgradable» для их показа.
    root@postgres2:~#
    root@postgres2:~# apt install postgresql-15                                                                                                                                                                                22:27:20 [72/1860]
    Чтение списков пакетов… Готово
    Построение дерева зависимостей… Готово
    Чтение информации о состоянии… Готово
    Будут установлены следующие дополнительные пакеты:
      libcommon-sense-perl libjson-perl libjson-xs-perl libllvm15 libpq5 libsensors-config libsensors5 libtypes-serialiser-perl postgresql-client-15 postgresql-client-common postgresql-common ssl-cert sysstat
    Предлагаемые пакеты:
      lm-sensors postgresql-doc-15 isag
    Следующие НОВЫЕ пакеты будут установлены:
      libcommon-sense-perl libjson-perl libjson-xs-perl libllvm15 libpq5 libsensors-config libsensors5 libtypes-serialiser-perl postgresql-15 postgresql-client-15 postgresql-client-common postgresql-common ssl-cert sysstat
    Обновлено 0 пакетов, установлено 14 новых пакетов, для удаления отмечено 0 пакетов, и 26 пакетов не обновлено.
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
    Получено 45,4 MB за 10с (4 407 kB/s)
    Предварительная настройка пакетов …
    Выбор ранее не выбранного пакета libjson-perl.
    (Чтение базы данных … на данный момент установлено 74623 файла и каталога.)
    Подготовка к распаковке …/00-libjson-perl_4.04000-1_all.deb …                                                                                                                                                              22:27:24 [40/1860]
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
    Scanning linux images...

    Running kernel seems to be up-to-date.

    No services need to be restarted.

    No containers need to be restarted.

    No user sessions are running outdated binaries.

    No VM guests are running outdated hypervisor (qemu) binaries on this host.
    root@postgres2:~#
    ```
  * Останавливаем postgresql
    ```
    root@postgres2:~# systemctl stop postgresql@15-main
    root@postgres2:~# ps -ef | grep postgres
    root        4362    1084  0 19:30 pts/1    00:00:00 grep --color=auto postgres
    root@postgres2:~#
    ```
  * Вычищаем /var/lib/postgresql, меняем data_directory в postgresql.conf
    ```
    root@postgres2:~# rm -r /var/lib/postgresql/*
    root@postgres2:~# ls -laF /var/lib/postgresql/
    total 8
    drwxr-xr-x  2 postgres postgres 4096 июн 24 19:31 ./
    drwxr-xr-x 41 root     root     4096 июн 24 19:27 ../
    root@postgres2:~# vim /etc/postgresql/15/main/postgresql.conf
    root@postgres2:~# grep data_directory /etc/postgresql/15/main/postgresql.conf
    #data_directory = '/var/lib/postgresql/15/main'         # use data in another directory
    data_directory = '/mnt/data/15/main'            # use data in another directory
    root@postgres2:~#
    ```
  * Перезагружаемся
    ```
    root@postgres2:~# reboot
    root@postgres2:~# Connection to postgres2.otus closed by remote host.
    Connection to postgres2.otus closed.
    [anton@manager ~]$
    ```
  * Проверяем ОС
    ```
    [anton@manager ~]$ ssh postgres2.otus
    anton@postgres2.otus's password:
    Welcome to Ubuntu 22.04.4 LTS (GNU/Linux 5.15.0-112-generic x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/pro

      System information as of Пн 24 июн 2024 19:33:04 UTC

      System load:  0.23388671875     Processes:               151
      Usage of /:   44.8% of 7.50GB   Users logged in:         0
      Memory usage: 6%                IPv4 address for enp0s3: 10.0.2.6
      Swap usage:   0%


    Расширенное поддержание безопасности (ESM) для Applications выключено.

    25 обновлений может быть применено немедленно.
    Чтобы просмотреть дополнительные обновления выполните: apt list --upgradable

    Включите ESM Apps для получения дополнительных будущих обновлений безопасности.
    Смотрите https://ubuntu.com/esm или выполните: sudo pro status


    Last login: Mon Jun 24 19:22:07 2024 from 10.0.2.4
    anton@postgres2:~$ systemctl --failed
      UNIT LOAD ACTIVE SUB DESCRIPTION
    0 loaded units listed.
    anton@postgres2:~$ ps -ef | grep postgres
    postgres     808       1  0 19:33 ?        00:00:00 /usr/lib/postgresql/15/bin/postgres -D /mnt/data/15/main -c config_file=/etc/postgresql/15/main/postgresql.conf
    postgres     809     808  0 19:33 ?        00:00:00 postgres: 15/main: checkpointer
    postgres     810     808  0 19:33 ?        00:00:00 postgres: 15/main: background writer
    postgres     812     808  0 19:33 ?        00:00:00 postgres: 15/main: walwriter
    postgres     813     808  0 19:33 ?        00:00:00 postgres: 15/main: autovacuum launcher
    postgres     814     808  0 19:33 ?        00:00:00 postgres: 15/main: logical replication launcher
    anton       1126    1114  0 19:33 pts/0    00:00:00 grep --color=auto postgres
    anton@postgres2:~$
    ```
  * Проверяем контрольную таблицу
    ```
    anton@postgres2:~$ sudo su - postgres -c psql
    [sudo] password for anton:
    psql (15.7 (Ubuntu 15.7-1.pgdg22.04+1))
    Введите "help", чтобы получить справку.

    postgres=# SELECT * FROM control_table;
     id |             dt
    ----+----------------------------
      1 | 2024-06-24 15:47:03.208952
    (1 строка)

    postgres=# INSERT INTO control_table (id, dt) VALUES (2, now());
    INSERT 0 1
    postgres=# SELECT * FROM control_table;
     id |             dt
    ----+----------------------------
      1 | 2024-06-24 15:47:03.208952
      2 | 2024-06-24 19:35:02.102847
    (2 строки)

    postgres=#
    ```

