**Описание/Пошаговая инструкция выполнения домашнего задания:**
* создать ВМ с Ubuntu 20.04/22.04 или развернуть докер любым удобным способом
  * Done
* поставить на нем Docker Engine
  * Done
    ```
    [anton@otusmanager ~]$ sudo pacman -S docker
    [sudo] пароль для anton:
    разрешение зависимостей...
    проверка конфликтов...

    Пакеты (5) bridge-utils-1.7.1-2  containerd-1.7.18-1  libtool-2.5.0+14+g9a4a0261-1  runc-1.1.13-1  docker-1:26.1.4-1

    Будет загружено:     58,18 MiB
    Будет установлено:  240,20 MiB
  
    :: Приступить к установке? [Y/n]
    :: Получение пакетов...
     docker-1:26.1.4-1-x86_64                                                                                           28,7 MiB   932 KiB/s 00:31 [#######################################################################################] 100%
     containerd-1.7.18-1-x86_64                                                                                         26,2 MiB   495 KiB/s 00:54 [#######################################################################################] 100%
     runc-1.1.13-1-x86_64                                                                                                2,9 MiB  1119 KiB/s 00:03 [#######################################################################################] 100%
     libtool-2.5.0+14+g9a4a0261-1-x86_64                                                                               409,1 KiB  1023 KiB/s 00:00 [#######################################################################################] 100%
     bridge-utils-1.7.1-2-x86_64                                                                                        16,2 KiB   284 KiB/s 00:00 [#######################################################################################] 100%
     Total (5/5)                                                                                                        58,2 MiB   669 KiB/s 01:29 [#######################################################################################] 100%
    (5/5) проверка ключей                                                                                                                          [#######################################################################################] 100%
    (5/5) проверка целостности пакета                                                                                                              [#######################################################################################] 100%
    (5/5) загрузка файлов пакетов                                                                                                                  [#######################################################################################] 100%
    (5/5) проверка конфликтов файлов                                                                                                               [#######################################################################################] 100%
    (5/5) проверка доступного места                                                                                                                [#######################################################################################] 100%
    :: Обработка изменений пакета...
    (1/5) установка bridge-utils                                                                                                                   [#######################################################################################] 100%
    (2/5) установка libtool                                                                                                                        [#######################################################################################] 100%
    (3/5) установка runc                                                                                                                           [#######################################################################################] 100%
    Дополнительные зависимости для 'runc'
        criu: checkpoint support
    (4/5) установка containerd                                                                                                                     [#######################################################################################] 100%
    (5/5) установка docker                                                                                                                         [#######################################################################################] 100%
    Дополнительные зависимости для 'docker'
        btrfs-progs: btrfs backend support
        pigz: parallel gzip compressor support
        docker-scan: vulnerability scanner
        docker-buildx: extended build capabilities
    :: Запуск post-transaction hooks...
    (1/5) Creating system user accounts...
    Creating group 'docker' with GID 969.
    (2/5) Reloading system manager configuration...
    (3/5) Reloading device manager configuration...
    (4/5) Arming ConditionNeedsUpdate...
    (5/5) Updating the info directory file...
    [anton@otusmanager ~]$ sudo systemctl start docker
    [anton@otusmanager ~]$ systemctl status docker
    ● docker.service - Docker Application Container Engine
         Loaded: loaded (/usr/lib/systemd/system/docker.service; disabled; preset: disabled)
         Active: active (running) since Thu 2024-06-20 17:56:13 MSK; 51s ago
     Invocation: f77497d0baf3411196309b6438fb8e49
    TriggeredBy: ● docker.socket
           Docs: https://docs.docker.com
       Main PID: 699 (dockerd)
          Tasks: 9
         Memory: 30.4M (peak: 32.2M)
            CPU: 210ms
         CGroup: /system.slice/docker.service
                 └─699 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock

    июн 20 17:56:08 otusmanager systemd[1]: Starting Docker Application Container Engine...
    июн 20 17:56:08 otusmanager dockerd[699]: time="2024-06-20T17:56:08.350302031+03:00" level=info msg="Starting up"
    июн 20 17:56:10 otusmanager dockerd[699]: time="2024-06-20T17:56:10.597489657+03:00" level=info msg="Loading containers: start."
    июн 20 17:56:12 otusmanager dockerd[699]: time="2024-06-20T17:56:12.122567077+03:00" level=info msg="Loading containers: done."
    июн 20 17:56:12 otusmanager dockerd[699]: time="2024-06-20T17:56:12.591519102+03:00" level=warning msg="Not using native diff for overlay2, this may cause degraded performance for building images: kernel has CONFIG_OVERLAY_FS_REDIRECT_D>
    июн 20 17:56:12 otusmanager dockerd[699]: time="2024-06-20T17:56:12.591617523+03:00" level=info msg="Docker daemon" commit=de5c9cf0b9 containerd-snapshotter=false storage-driver=overlay2 version=26.1.4
    июн 20 17:56:12 otusmanager dockerd[699]: time="2024-06-20T17:56:12.591715462+03:00" level=info msg="Daemon has completed initialization"
    июн 20 17:56:13 otusmanager dockerd[699]: time="2024-06-20T17:56:13.266347675+03:00" level=info msg="API listen on /run/docker.sock"
    июн 20 17:56:13 otusmanager systemd[1]: Started Docker Application Container Engine.
    [anton@otusmanager ~]$
    ```
* сделать каталог /var/lib/postgresql
  * Done
    ```
    [anton@otusmanager ~]$ sudo mkdir -v /var/lib/postgresql
    mkdir: создан каталог '/var/lib/postgresql'
    [anton@otusmanager ~]$
    ```
* развернуть контейнер с PostgreSQL 15 смонтировав в него /var/lib/postgresql
  * Запустим ванильку для начала
    ```
    [anton@otusmanager ~]$ sudo docker run -d --name otus_postgres postgres
    Unable to find image 'postgres:latest' locally
    latest: Pulling from library/postgres
    2cc3ae149d28: Pull complete
    d1a63825d58e: Pull complete
    ed6f372fe58d: Pull complete
    35f975e69306: Pull complete
    40c4fe86e99d: Pull complete
    4795e1a32ff6: Pull complete
    bcb5a54ae87d: Pull complete
    d3983228bec6: Pull complete
    5378bf7229e9: Pull complete
    bba3241011a6: Pull complete
    5e1d0413d05a: Pull complete
    6a489170d05e: Pull complete
    440b39aff272: Pull complete
    582c79113570: Pull complete
    Digest: sha256:46aa2ee5d664b275f05d1a963b30fff60fb422b4b594d509765c42db46d48881
    Status: Downloaded newer image for postgres:latest
    4b0242b458bff8ebf81797b8265f27f03154dfade0047c65f762cd82fc2c44c7
    [anton@otusmanager ~]$ sudo docker ps
    CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
    [anton@otusmanager ~]$ sudo docker ps -a
    CONTAINER ID   IMAGE      COMMAND                  CREATED          STATUS                      PORTS     NAMES
    4b0242b458bf   postgres   "docker-entrypoint.s…"   11 minutes ago   Exited (1) 11 minutes ago             otus_postgres
    ```
  * Не стартует. Хм, ещё раз?
    ```
    [anton@otusmanager ~]$ sudo docker start otus_postgres
    otus_postgres
    [anton@otusmanager ~]$ sudo docker ps
    CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
    ```
  * Удалим, запустим "интерактивно"
    ```
    [anton@otusmanager ~]$ sudo docker rm otus_postgres
    otus_postgres
    [anton@otusmanager ~]$ sudo docker ps -a
    CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
    [anton@otusmanager ~]$ sudo docker run --name otus_postgres postgres
    Error: Database is uninitialized and superuser password is not specified.
           You must specify POSTGRES_PASSWORD to a non-empty value for the
           superuser. For example, "-e POSTGRES_PASSWORD=password" on "docker run".

           You may also use "POSTGRES_HOST_AUTH_METHOD=trust" to allow all
           connections without a password. This is *not* recommended.

           See PostgreSQL documentation about "trust":
           https://www.postgresql.org/docs/current/auth-trust.html
    [anton@otusmanager ~]$ sudo docker rm otus_postgres
    otus_postgres
    ```
  * Ага, хочет пароль. Сделаем. И удалять лениво, пусть сам чистит.
    ```
    [anton@otusmanager ~]$ sudo docker run --rm --name otus_postgres -e "POSTGRES_PASSWORD=secret" postgres
    The files belonging to this database system will be owned by user "postgres".
    This user must also own the server process.

    The database cluster will be initialized with locale "en_US.utf8".
    The default database encoding has accordingly been set to "UTF8".
    The default text search configuration will be set to "english".

    Data page checksums are disabled.

    fixing permissions on existing directory /var/lib/postgresql/data ... ok
    creating subdirectories ... ok
    selecting dynamic shared memory implementation ... posix
    selecting default max_connections ... 100
    selecting default shared_buffers ... 128MB
    selecting default time zone ... Etc/UTC
    creating configuration files ... ok
    running bootstrap script ... ok
    performing post-bootstrap initialization ... ok
    syncing data to disk ... ok


    Success. You can now start the database server using:

        pg_ctl -D /var/lib/postgresql/data -l logfile start

    initdb: warning: enabling "trust" authentication for local connections
    initdb: hint: You can change this by editing pg_hba.conf or using the option -A, or --auth-local and --auth-host, the next time you run initdb.
    waiting for server to start....2024-06-20 15:34:32.067 UTC [46] LOG:  starting PostgreSQL 16.3 (Debian 16.3-1.pgdg120+1) on x86_64-pc-linux-gnu, compiled by gcc (Debian 12.2.0-14) 12.2.0, 64-bit
    2024-06-20 15:34:32.119 UTC [46] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
    2024-06-20 15:34:32.248 UTC [49] LOG:  database system was shut down at 2024-06-20 15:33:51 UTC
    2024-06-20 15:34:32.311 UTC [46] LOG:  database system is ready to accept connections
     done
    server started

    /usr/local/bin/docker-entrypoint.sh: ignoring /docker-entrypoint-initdb.d/*

    waiting for server to shut down....2024-06-20 15:34:32.482 UTC [46] LOG:  received fast shutdown request
    2024-06-20 15:34:32.597 UTC [46] LOG:  aborting any active transactions
    2024-06-20 15:34:32.599 UTC [46] LOG:  background worker "logical replication launcher" (PID 52) exited with exit code 1
    2024-06-20 15:34:32.599 UTC [47] LOG:  shutting down
    2024-06-20 15:34:32.637 UTC [47] LOG:  checkpoint starting: shutdown immediate
    2024-06-20 15:34:32.892 UTC [47] LOG:  checkpoint complete: wrote 3 buffers (0.0%); 0 WAL file(s) added, 0 removed, 0 recycled; write=0.052 s, sync=0.030 s, total=0.293 s; sync files=2, longest=0.015 s, average=0.015 s; distance=0 kB, es
    timate=0 kB; lsn=0/14EA208, redo lsn=0/14EA208
    2024-06-20 15:34:32.894 UTC [46] LOG:  database system is shut down
     done
    server stopped

    PostgreSQL init process complete; ready for start up.

    2024-06-20 15:34:33.019 UTC [1] LOG:  starting PostgreSQL 16.3 (Debian 16.3-1.pgdg120+1) on x86_64-pc-linux-gnu, compiled by gcc (Debian 12.2.0-14) 12.2.0, 64-bit
    2024-06-20 15:34:33.020 UTC [1] LOG:  listening on IPv4 address "0.0.0.0", port 5432
    2024-06-20 15:34:33.020 UTC [1] LOG:  listening on IPv6 address "::", port 5432
    2024-06-20 15:34:33.096 UTC [1] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5432"
    2024-06-20 15:34:33.196 UTC [60] LOG:  database system was shut down at 2024-06-20 15:34:32 UTC
    2024-06-20 15:34:33.271 UTC [1] LOG:  database system is ready to accept connections
    ```
  * Запустился, работает. Откроем вторую консоль, остановим
    ```
    [anton@otusmanager ~]$ sudo docker ps
    [sudo] пароль для anton:
    CONTAINER ID   IMAGE      COMMAND                  CREATED         STATUS         PORTS      NAMES
    10933430c8ba   postgres   "docker-entrypoint.s…"   8 minutes ago   Up 8 minutes   5432/tcp   otus_postgres
    [anton@otusmanager ~]$ sudo docker stop otus_postgres
    otus_postgres
    [anton@otusmanager ~]$
    [anton@otusmanager ~]$ sudo docker ps -a
    CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
    [anton@otusmanager ~]$
    ─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
    2024-06-20 15:39:33.313 UTC [58] LOG:  checkpoint starting: time
    2024-06-20 15:39:40.388 UTC [58] LOG:  checkpoint complete: wrote 44 buffers (0.3%); 0 WAL file(s) added, 0 removed, 0 recycled; write=6.036 s, sync=0.784 s, total=7.075 s; sync files=11, longest=0.716 s, average=0.072 s; distance=261 kB, estimate=261 kB; lsn=0/152B660, redo lsn=0/152B628

    2024-06-20 15:42:49.386 UTC [1] LOG:  received fast shutdown request
    2024-06-20 15:42:49.441 UTC [1] LOG:  aborting any active transactions
    2024-06-20 15:42:49.443 UTC [1] LOG:  background worker "logical replication launcher" (PID 63) exited with exit code 1
    2024-06-20 15:42:49.443 UTC [58] LOG:  shutting down
    2024-06-20 15:42:49.478 UTC [58] LOG:  checkpoint starting: shutdown immediate
    2024-06-20 15:42:50.570 UTC [58] LOG:  checkpoint complete: wrote 0 buffers (0.0%); 0 WAL file(s) added, 0 removed, 0 recycled; write=0.001 s, sync=0.001 s, total=1.127 s; sync files=0, longest=0.000 s, average=0.000 s; distance=0 kB, estimate=234 kB; lsn=0/152B710, redo lsn=0/152B710
    2024-06-20 15:42:50.573 UTC [1] LOG:  database system is shut down
    ```
  * Вспоминаем задание... Нам нужно пробросить /var/lib/postgresql и версия должна быть 15. Дополнительно - сразу пробросим порт
    ```
    [anton@otusmanager ~]$ sudo docker run \
    >                                 --rm \
    >                                 --name otus_postgres \
    >                                 -e "POSTGRES_PASSWORD=secret" \
    >                                 -d \
    >                                 -p 5432:5432 \
    >                                 -v /var/lib/postgresql:/var/lib/postgresql \
    >                                     postgres:15
    Unable to find image 'postgres:15' locally
    15: Pulling from library/postgres
    2cc3ae149d28: Already exists
    268fb554018f: Pull complete
    b99953dbc0e0: Pull complete
    ec644a5562df: Pull complete
    f5b3633ef3dc: Pull complete
    5bcaade78f8f: Pull complete
    d27a57b6d3a6: Pull complete
    de5003265405: Pull complete
    df7ad6c21ee9: Pull complete
    259be144ed37: Pull complete
    f6febd76974e: Pull complete
    fb5c51699243: Pull complete
    861bc5810e64: Pull complete
    0d922d3c7d7b: Pull complete
    Digest: sha256:2d8059176e789c849701fc06d3f7834d4f6db52b74c89f7f3517c4540a3c38bd
    Status: Downloaded newer image for postgres:15
    bf8174905fc3f7ec7896c9b5ecabb43319b0e8d585f146022f2b7aba9877990e
    [anton@otusmanager ~]$ sudo docker ps
    CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS          PORTS                                       NAMES
    bf8174905fc3   postgres:15   "docker-entrypoint.s…"   19 seconds ago   Up 14 seconds   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   otus_postgres
    ```
  * Проверим хост-директорию
    ```
    [anton@otusmanager ~]$ ls -laF /var/lib/postgresql/
    итого 12
    drwxr-xr-x  3 root root 4096 июн 20 18:58 ./
    drwxr-xr-x 20 root root 4096 июн 20 17:59 ../
    drwxr-xr-x  2 root root 4096 июн 20 18:58 data/
    [anton@otusmanager ~]$
    ```
* развернуть контейнер с клиентом postgres
  * Не стильно, но в лоб. Запустим образ Ubuntu
    ```
    [anton@otusmanager ~]$ sudo docker run -d -it --name otus_psql ubuntu
    Unable to find image 'ubuntu:latest' locally
    latest: Pulling from library/ubuntu
    9c704ecd0c69: Pull complete
    Digest: sha256:2e863c44b718727c860746568e1d54afd13b2fa71b160f5cd9058fc436217b30
    Status: Downloaded newer image for ubuntu:latest
    5f778bf962866e4678474d6afd89f405dc4361c8204dc775999a64a3894cef88
    [anton@otusmanager ~]$ sudo docker ps -a
    CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS          PORTS                                       NAMES
    5f778bf96286   ubuntu        "/bin/bash"              3 minutes ago    Up 2 minutes                                                otus_psql
    bf8174905fc3   postgres:15   "docker-entrypoint.s…"   49 minutes ago   Up 49 minutes   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   otus_postgres
    ```
  * Зайдём в контейнер с Ubuntu, установим туда postgresql-client
    ```
    [anton@otusmanager ~]$ sudo docker exec -it otus_psql bash
    root@5f778bf96286:/# apt update
    Get:1 http://security.ubuntu.com/ubuntu noble-security InRelease [126 kB]
    Get:2 http://archive.ubuntu.com/ubuntu noble InRelease [256 kB]
    Get:3 http://security.ubuntu.com/ubuntu noble-security/main amd64 Packages [202 kB]
    Get:4 http://security.ubuntu.com/ubuntu noble-security/restricted amd64 Packages [84.8 kB]
    Get:5 http://security.ubuntu.com/ubuntu noble-security/universe amd64 Packages [65.1 kB]
    Get:6 http://archive.ubuntu.com/ubuntu noble-updates InRelease [126 kB]
    Get:7 http://archive.ubuntu.com/ubuntu noble-backports InRelease [126 kB]
    Get:8 http://archive.ubuntu.com/ubuntu noble/restricted amd64 Packages [117 kB]
    Get:9 http://archive.ubuntu.com/ubuntu noble/main amd64 Packages [1808 kB]
    Get:10 http://archive.ubuntu.com/ubuntu noble/multiverse amd64 Packages [331 kB]
    Get:11 http://archive.ubuntu.com/ubuntu noble/universe amd64 Packages [19.3 MB]
    Get:12 http://archive.ubuntu.com/ubuntu noble-updates/multiverse amd64 Packages [15.5 kB]
    Get:13 http://archive.ubuntu.com/ubuntu noble-updates/universe amd64 Packages [92.4 kB]
    Get:14 http://archive.ubuntu.com/ubuntu noble-updates/restricted amd64 Packages [139 kB]
    Get:15 http://archive.ubuntu.com/ubuntu noble-updates/main amd64 Packages [226 kB]
    Get:16 http://archive.ubuntu.com/ubuntu noble-backports/universe amd64 Packages [7519 B]
    Fetched 23.0 MB in 42s (554 kB/s)
    Reading package lists... Done
    Building dependency tree... Done
    Reading state information... Done
    All packages are up to date.
    root@5f778bf96286:/#
    root@5f778bf96286:/#
    root@5f778bf96286:/#
    root@5f778bf96286:/#
    root@5f778bf96286:/#
    root@5f778bf96286:/# apt install postgresql-client                                                                                                                                                                          19:53:37 [79/832]
    Reading package lists... Done
    Building dependency tree... Done
    Reading state information... Done
    The following additional packages will be installed:
      krb5-locales libgdbm-compat4t64 libgdbm6t64 libgssapi-krb5-2 libk5crypto3 libkeyutils1 libkrb5-3 libkrb5support0 libldap-common libldap2 libperl5.38t64 libpq5 libreadline8t64 libsasl2-2 libsasl2-modules libsasl2-modules-db netbase
      perl perl-modules-5.38 postgresql-client-16 postgresql-client-common readline-common
    Suggested packages:
      gdbm-l10n krb5-doc krb5-user libsasl2-modules-gssapi-mit | libsasl2-modules-gssapi-heimdal libsasl2-modules-ldap libsasl2-modules-otp libsasl2-modules-sql perl-doc libterm-readline-gnu-perl | libterm-readline-perl-perl make
      libtap-harness-archive-perl postgresql-16 postgresql-doc-16 readline-doc
    The following NEW packages will be installed:
      krb5-locales libgdbm-compat4t64 libgdbm6t64 libgssapi-krb5-2 libk5crypto3 libkeyutils1 libkrb5-3 libkrb5support0 libldap-common libldap2 libperl5.38t64 libpq5 libreadline8t64 libsasl2-2 libsasl2-modules libsasl2-modules-db netbase
      perl perl-modules-5.38 postgresql-client postgresql-client-16 postgresql-client-common readline-common
    0 upgraded, 23 newly installed, 0 to remove and 0 not upgraded.
    Need to get 10.9 MB of archives.
    After this operation, 58.9 MB of additional disk space will be used.
    Do you want to continue? [Y/n]
    Get:1 http://archive.ubuntu.com/ubuntu noble/main amd64 perl-modules-5.38 all 5.38.2-3.2build2 [3110 kB]
    Get:2 http://archive.ubuntu.com/ubuntu noble/main amd64 libgdbm6t64 amd64 1.23-5.1build1 [34.4 kB]
    Get:3 http://archive.ubuntu.com/ubuntu noble/main amd64 libgdbm-compat4t64 amd64 1.23-5.1build1 [6710 B]
    Get:4 http://archive.ubuntu.com/ubuntu noble/main amd64 libperl5.38t64 amd64 5.38.2-3.2build2 [4873 kB]
    Get:5 http://archive.ubuntu.com/ubuntu noble/main amd64 perl amd64 5.38.2-3.2build2 [231 kB]
    Get:6 http://archive.ubuntu.com/ubuntu noble/main amd64 krb5-locales all 1.20.1-6ubuntu2 [13.8 kB]
    Get:7 http://archive.ubuntu.com/ubuntu noble/main amd64 libkrb5support0 amd64 1.20.1-6ubuntu2 [33.5 kB]
    Get:8 http://archive.ubuntu.com/ubuntu noble/main amd64 libk5crypto3 amd64 1.20.1-6ubuntu2 [81.7 kB]
    Get:9 http://archive.ubuntu.com/ubuntu noble/main amd64 libkeyutils1 amd64 1.6.3-3build1 [9490 B]
    Get:10 http://archive.ubuntu.com/ubuntu noble/main amd64 libkrb5-3 amd64 1.20.1-6ubuntu2 [347 kB]
    Get:11 http://archive.ubuntu.com/ubuntu noble/main amd64 libgssapi-krb5-2 amd64 1.20.1-6ubuntu2 [142 kB]
    Get:12 http://archive.ubuntu.com/ubuntu noble/main amd64 readline-common all 8.2-4build1 [56.5 kB]
    Get:13 http://archive.ubuntu.com/ubuntu noble/main amd64 libreadline8t64 amd64 8.2-4build1 [153 kB]
    Get:14 http://archive.ubuntu.com/ubuntu noble/main amd64 netbase all 6.4 [13.1 kB]
    Get:15 http://archive.ubuntu.com/ubuntu noble/main amd64 libldap-common all 2.6.7+dfsg-1~exp1ubuntu8 [31.4 kB]
    Get:16 http://archive.ubuntu.com/ubuntu noble/main amd64 libsasl2-modules-db amd64 2.1.28+dfsg1-5ubuntu3 [20.3 kB]
    Get:17 http://archive.ubuntu.com/ubuntu noble/main amd64 libsasl2-2 amd64 2.1.28+dfsg1-5ubuntu3 [53.2 kB]
    Get:18 http://archive.ubuntu.com/ubuntu noble/main amd64 libldap2 amd64 2.6.7+dfsg-1~exp1ubuntu8 [195 kB]
    Get:19 http://archive.ubuntu.com/ubuntu noble-updates/main amd64 libpq5 amd64 16.3-0ubuntu0.24.04.1 [141 kB]
    Get:20 http://archive.ubuntu.com/ubuntu noble/main amd64 libsasl2-modules amd64 2.1.28+dfsg1-5ubuntu3 [69.7 kB]
    Get:21 http://archive.ubuntu.com/ubuntu noble/main amd64 postgresql-client-common all 257build1 [36.3 kB]
    Get:22 http://archive.ubuntu.com/ubuntu noble-updates/main amd64 postgresql-client-16 amd64 16.3-0ubuntu0.24.04.1 [1269 kB]
    Get:23 http://archive.ubuntu.com/ubuntu noble/main amd64 postgresql-client all 16+257build1 [11.5 kB]
    Fetched 10.9 MB in 16s (678 kB/s)
    debconf: delaying package configuration, since apt-utils is not installed
    Selecting previously unselected package perl-modules-5.38.
    (Reading database ... 4376 files and directories currently installed.)
    Preparing to unpack .../00-perl-modules-5.38_5.38.2-3.2build2_all.deb ...
    Unpacking perl-modules-5.38 (5.38.2-3.2build2) ...
    Selecting previously unselected package libgdbm6t64:amd64.
    Preparing to unpack .../01-libgdbm6t64_1.23-5.1build1_amd64.deb ...
    Unpacking libgdbm6t64:amd64 (1.23-5.1build1) ...
    Selecting previously unselected package libgdbm-compat4t64:amd64.
    Preparing to unpack .../02-libgdbm-compat4t64_1.23-5.1build1_amd64.deb ...
    Unpacking libgdbm-compat4t64:amd64 (1.23-5.1build1) ...
    Selecting previously unselected package libperl5.38t64:amd64.
    Preparing to unpack .../03-libperl5.38t64_5.38.2-3.2build2_amd64.deb ...
    Unpacking libperl5.38t64:amd64 (5.38.2-3.2build2) ...
    Selecting previously unselected package perl.
    Preparing to unpack .../04-perl_5.38.2-3.2build2_amd64.deb ...
    Unpacking perl (5.38.2-3.2build2) ...
    Selecting previously unselected package krb5-locales.
    Preparing to unpack .../05-krb5-locales_1.20.1-6ubuntu2_all.deb ...
    Unpacking krb5-locales (1.20.1-6ubuntu2) ...
    Selecting previously unselected package libkrb5support0:amd64.
    Preparing to unpack .../06-libkrb5support0_1.20.1-6ubuntu2_amd64.deb ...
    Unpacking libkrb5support0:amd64 (1.20.1-6ubuntu2) ...
    Selecting previously unselected package libk5crypto3:amd64.
    Preparing to unpack .../07-libk5crypto3_1.20.1-6ubuntu2_amd64.deb ...
    Unpacking libk5crypto3:amd64 (1.20.1-6ubuntu2) ...
    Selecting previously unselected package libkeyutils1:amd64.
    Preparing to unpack .../08-libkeyutils1_1.6.3-3build1_amd64.deb ...
    Unpacking libkeyutils1:amd64 (1.6.3-3build1) ...
    Selecting previously unselected package libkrb5-3:amd64.
    Preparing to unpack .../09-libkrb5-3_1.20.1-6ubuntu2_amd64.deb ...
    Unpacking libkrb5-3:amd64 (1.20.1-6ubuntu2) ...
    Selecting previously unselected package libgssapi-krb5-2:amd64.
    Preparing to unpack .../10-libgssapi-krb5-2_1.20.1-6ubuntu2_amd64.deb ...
    Unpacking libgssapi-krb5-2:amd64 (1.20.1-6ubuntu2) ...
    Selecting previously unselected package readline-common.
    Preparing to unpack .../11-readline-common_8.2-4build1_all.deb ...
    Unpacking readline-common (8.2-4build1) ...
    Selecting previously unselected package libreadline8t64:amd64.
    Preparing to unpack .../12-libreadline8t64_8.2-4build1_amd64.deb ...
    Adding 'diversion of /lib/x86_64-linux-gnu/libhistory.so.8 to /lib/x86_64-linux-gnu/libhistory.so.8.usr-is-merged by libreadline8t64'
    Adding 'diversion of /lib/x86_64-linux-gnu/libhistory.so.8.2 to /lib/x86_64-linux-gnu/libhistory.so.8.2.usr-is-merged by libreadline8t64'
    Adding 'diversion of /lib/x86_64-linux-gnu/libreadline.so.8 to /lib/x86_64-linux-gnu/libreadline.so.8.usr-is-merged by libreadline8t64'
    Adding 'diversion of /lib/x86_64-linux-gnu/libreadline.so.8.2 to /lib/x86_64-linux-gnu/libreadline.so.8.2.usr-is-merged by libreadline8t64'
    Unpacking libreadline8t64:amd64 (8.2-4build1) ...
    Selecting previously unselected package netbase.
    Preparing to unpack .../13-netbase_6.4_all.deb ...
    Unpacking netbase (6.4) ...
    Selecting previously unselected package libldap-common.
    Preparing to unpack .../14-libldap-common_2.6.7+dfsg-1~exp1ubuntu8_all.deb ...
    Unpacking libldap-common (2.6.7+dfsg-1~exp1ubuntu8) ...
    Selecting previously unselected package libsasl2-modules-db:amd64.
    Preparing to unpack .../15-libsasl2-modules-db_2.1.28+dfsg1-5ubuntu3_amd64.deb ...
    Unpacking libsasl2-modules-db:amd64 (2.1.28+dfsg1-5ubuntu3) ...
    Selecting previously unselected package libsasl2-2:amd64.
    Preparing to unpack .../16-libsasl2-2_2.1.28+dfsg1-5ubuntu3_amd64.deb ...
    Unpacking libsasl2-2:amd64 (2.1.28+dfsg1-5ubuntu3) ...
    Selecting previously unselected package libldap2:amd64.
    Preparing to unpack .../17-libldap2_2.6.7+dfsg-1~exp1ubuntu8_amd64.deb ...
    Unpacking libldap2:amd64 (2.6.7+dfsg-1~exp1ubuntu8) ...
    Selecting previously unselected package libpq5:amd64.
    Preparing to unpack .../18-libpq5_16.3-0ubuntu0.24.04.1_amd64.deb ...
    Unpacking libpq5:amd64 (16.3-0ubuntu0.24.04.1) ...
    Selecting previously unselected package libsasl2-modules:amd64.
    Preparing to unpack .../19-libsasl2-modules_2.1.28+dfsg1-5ubuntu3_amd64.deb ...
    Unpacking libsasl2-modules:amd64 (2.1.28+dfsg1-5ubuntu3) ...
    Selecting previously unselected package postgresql-client-common.
    Preparing to unpack .../20-postgresql-client-common_257build1_all.deb ...
    Unpacking postgresql-client-common (257build1) ...
    Selecting previously unselected package postgresql-client-16.
    Preparing to unpack .../21-postgresql-client-16_16.3-0ubuntu0.24.04.1_amd64.deb ...
    Unpacking postgresql-client-16 (16.3-0ubuntu0.24.04.1) ...
    Selecting previously unselected package postgresql-client.
    Preparing to unpack .../22-postgresql-client_16+257build1_all.deb ...
    Unpacking postgresql-client (16+257build1) ...
    Setting up libkeyutils1:amd64 (1.6.3-3build1) ...
    Setting up libgdbm6t64:amd64 (1.23-5.1build1) ...
    Setting up libgdbm-compat4t64:amd64 (1.23-5.1build1) ...
    Setting up libsasl2-modules:amd64 (2.1.28+dfsg1-5ubuntu3) ...
    Setting up krb5-locales (1.20.1-6ubuntu2) ...
    Setting up libldap-common (2.6.7+dfsg-1~exp1ubuntu8) ...
    Setting up libkrb5support0:amd64 (1.20.1-6ubuntu2) ...
    Setting up libsasl2-modules-db:amd64 (2.1.28+dfsg1-5ubuntu3) ...
    Setting up perl-modules-5.38 (5.38.2-3.2build2) ...
    Setting up libk5crypto3:amd64 (1.20.1-6ubuntu2) ...
    Setting up libsasl2-2:amd64 (2.1.28+dfsg1-5ubuntu3) ...
    Setting up netbase (6.4) ...
    Setting up libkrb5-3:amd64 (1.20.1-6ubuntu2) ...
    Setting up libperl5.38t64:amd64 (5.38.2-3.2build2) ...
    Setting up readline-common (8.2-4build1) ...
    Setting up libldap2:amd64 (2.6.7+dfsg-1~exp1ubuntu8) ...
    Setting up perl (5.38.2-3.2build2) ...
    Setting up libgssapi-krb5-2:amd64 (1.20.1-6ubuntu2) ...
    Setting up libreadline8t64:amd64 (8.2-4build1) ...
    Setting up postgresql-client-common (257build1) ...
    Setting up libpq5:amd64 (16.3-0ubuntu0.24.04.1) ...
    Setting up postgresql-client-16 (16.3-0ubuntu0.24.04.1) ...
    update-alternatives: using /usr/share/postgresql/16/man/man1/psql.1.gz to provide /usr/share/man/man1/psql.1.gz (psql.1.gz) in auto mode
    Setting up postgresql-client (16+257build1) ...
    Processing triggers for libc-bin (2.39-0ubuntu8.2) ...
    root@5f778bf96286:/#
    ```
* подключится из контейнера с клиентом к контейнеру с сервером и сделать таблицу с парой строк
  * Done
    ```
    root@5f778bf96286:/# psql -h 192.168.1.1 -U postgres
    Password for user postgres:
    psql (16.3 (Ubuntu 16.3-0ubuntu0.24.04.1), server 15.7 (Debian 15.7-1.pgdg120+1))
    Type "help" for help.

    postgres=# CREATE TABLE control_table (id INT, dt TIMESTAMP);
    CREATE TABLE
    postgres=# INSERT INTO control_table (id, dt) VALUES (1, now());
    INSERT 0 1
    postgres=# SELECT * FROM control_table;
     id |             dt
    ----+----------------------------
      1 | 2024-06-20 19:30:48.694327
    (1 row)

    postgres=# INSERT INTO control_table (id, dt) VALUES (2, '2023-12-31 15:20:00.012354');
    INSERT 0 1
    postgres=# SELECT * FROM control_table;
     id |             dt
    ----+----------------------------
      1 | 2024-06-20 19:30:48.694327
      2 | 2023-12-31 15:20:00.012354
    (2 rows)

    postgres=#
    ```
* подключится к контейнеру с сервером с ноутбука/компьютера извне инстансов GCP/ЯО/места установки докера
  * Done
    ```
    [anton@ManageServer HW_02 Installation PostgreSQL in Docker]$ ip a | grep 192
        inet 192.168.1.51/24 brd 192.168.1.255 scope global eno1
    [anton@ManageServer HW_01 SQL, Relations, Intra in PostgreSQL]$ psql -h 192.168.1.1 -U postgres
    Пароль пользователя postgres:
    psql (16.2, сервер 15.7 (Debian 15.7-1.pgdg120+1))
    Введите "help", чтобы получить справку.

    postgres=# select * from control_table;
     id |             dt
    ----+----------------------------
      1 | 2024-06-20 20:12:23.283954
      2 | 2023-12-31 15:28:47.56129
    (2 строки)

    postgres=#
    ```
* удалить контейнер с сервером
  * Done
    ```
    [anton@otusmanager ~]$ sudo docker stop otus_postgres
    [sudo] пароль для anton:
    otus_postgres
    [anton@otusmanager ~]$ sudo docker ps -a
    CONTAINER ID   IMAGE     COMMAND       CREATED       STATUS       PORTS     NAMES
    5f778bf96286   ubuntu    "/bin/bash"   3 hours ago   Up 3 hours             otus_psql
    [anton@otusmanager ~]$
    ```
* создать его заново
  * Done
    ```
    [anton@otusmanager ~]$ sudo docker run \
    >                                 --rm \
    >                                 --name otus_postgres \
    >                                 -e "POSTGRES_PASSWORD=secret" \
    >                                 -d \
    >                                 -p 5432:5432 \
    >                                 -v /var/lib/postgresql:/var/lib/postgresql \
    >                                       postgres:15
    c3a968db21e054f68b177ea372595ee2dcda4c8223aee0e086779666856c8cf1
    [anton@otusmanager ~]$ sudo docker ps -a
    CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS         PORTS                                       NAMES
    c3a968db21e0   postgres:15   "docker-entrypoint.s…"   7 seconds ago   Up 4 seconds   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   otus_postgres
    5f778bf96286   ubuntu        "/bin/bash"              3 hours ago     Up 3 hours                                                 otus_psql
    [anton@otusmanager ~]$
    ```
* подключится снова из контейнера с клиентом к контейнеру с сервером
  * Done
    ```
    [anton@otusmanager ~]$ sudo docker exec -it otus_psql bash
    root@5f778bf96286:/# psql -h 192.168.1.1 -U postgres
    Password for user postgres:
    psql (16.3 (Ubuntu 16.3-0ubuntu0.24.04.1), server 15.7 (Debian 15.7-1.pgdg120+1))
    Type "help" for help.

    postgres=#
    ```
* проверить, что данные остались на месте
  * Проверяем
    ```
    postgres=# SELECT * FROM control_table;
    ERROR:  relation "control_table" does not exist
    LINE 1: SELECT * FROM control_table;
                      ^
    postgres=#
    ```
  * Разочарование, недопонимание, грусть, тоска...
  * Читаем документашку https://hub.docker.com/_/postgres
  * Перезапускаем СУБД с правильным пробросом директории и переопределением PGDATA
    ```
    [anton@otusmanager ~]$ sudo docker stop otus_postgres
    [sudo] пароль для anton:
    otus_postgres
    [anton@otusmanager ~]$ sudo docker ps -a
    CONTAINER ID   IMAGE     COMMAND       CREATED       STATUS       PORTS     NAMES
    5f778bf96286   ubuntu    "/bin/bash"   3 hours ago   Up 3 hours             otus_psql
    [anton@otusmanager ~]$ sudo docker run \
    >                                 --rm \
    >                                 --name otus_postgres \
    >                                 -e "POSTGRES_PASSWORD=secret" \
    >                                 -e PGDATA=/var/lib/postgresql/data/pgdata \
    >                                 -v /var/lib/postgresql:/var/lib/postgresql/data \
    >                                 -d \
    >                                 -p 5432:5432 \
    >                                       postgres:15
    01434bb36b64a99d0752ed71b519d9e99e3bcdc900822985c53e54ead12d7d61
    ```
  * Ещё раз заходим и создаём контрольную таблицу
    ```
    [anton@otusmanager ~]$ sudo docker exec -it otus_psql bash
    root@5f778bf96286:/# psql -h 192.168.1.1 -U postgres
    Password for user postgres:
    psql (16.3 (Ubuntu 16.3-0ubuntu0.24.04.1), server 15.7 (Debian 15.7-1.pgdg120+1))
    Type "help" for help.

    postgres=# CREATE TABLE control_table (id INT, dt TIMESTAMP);
    CREATE TABLE
    postgres=# INSERT INTO control_table (id, dt) VALUES (1, now()), (2, '2023-12-31 15:28:47.56129');
    INSERT 0 2
    postgres=# SELECT * FROM control_table;
     id |             dt
    ----+----------------------------
      1 | 2024-06-20 20:12:23.283954
      2 | 2023-12-31 15:28:47.56129
    (2 rows)

    postgres=#
    ```
  * Удаляем контейнер с СУБД, запускаем новый
    ```
    [anton@otusmanager ~]$ sudo docker stop otus_postgres
    otus_postgres
    [anton@otusmanager ~]$ sudo docker ps -a
    CONTAINER ID   IMAGE     COMMAND       CREATED       STATUS       PORTS     NAMES
    5f778bf96286   ubuntu    "/bin/bash"   3 hours ago   Up 3 hours             otus_psql
    [anton@otusmanager ~]$ sudo docker run \
    >                                 --rm \
    >                                 --name otus_postgres \
    >                                 -e "POSTGRES_PASSWORD=secret" \
    >                                 -e PGDATA=/var/lib/postgresql/data/pgdata \
    >                                 -v /var/lib/postgresql:/var/lib/postgresql/data \
    >                                 -p 5432:5432 \
    >                                 -d \
    >                                       postgres:15
    35d553eada8a15c179b0e5ced345bddffd59b15dc7b5797d9524c46db2c8d25d
    ```
  * Попытка № 2...
    ```
    [anton@otusmanager ~]$ sudo docker exec -it otus_psql bash
    root@5f778bf96286:/# psql -h 192.168.1.1 -U postgres
    Password for user postgres:
    psql (16.3 (Ubuntu 16.3-0ubuntu0.24.04.1), server 15.7 (Debian 15.7-1.pgdg120+1))
    Type "help" for help.

    postgres=# SELECT * FROM control_table;
     id |             dt
    ----+----------------------------
      1 | 2024-06-20 20:12:23.283954
      2 | 2023-12-31 15:28:47.56129
    (2 rows)

    postgres=#
    ```
  * Получилось

