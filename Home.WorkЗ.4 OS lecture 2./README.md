# Домашнее задание к занятию "3.4. Операционные системы, лекция 2"

#### 1.На лекции мы познакомились с [node_exporter](https://github.com/prometheus/node_exporter/releases). В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой [unit-файл](https://www.freedesktop.org/software/systemd/man/systemd.service.html) для node_exporter:

  * поместите его в автозагрузку,
  * предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на `systemctl cat cron`),
  * удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.
#### Ответ:

Установил node_exporter-1.3.1; 
Создаю небольшой скрипт (scriptforexporter), помещаю его в /usr/local/bin и делаем его исполняемым (sudo chmod +x /usr/local/bin/scriptforexporter)
Cоздаю unit file (node_exporter.service) для нового сервиса с таким содержимым:

Ответ на уточнение как именно в службу будут передаваться дополнительные опции:
Я так понял, что необходимо использовать EnvironmentFile, а точнее создать /etc/default/node_exporter, и тогда можно добавлять переменные среды нашего бинарного файла scriptforexporter через этот файл. Например, если мы хотим отключить несколько наборов метрик или включить больше наборов метрик, мы можем управлять этим файлом.

> sudo cat /etc/default/node_exporter

> OPTIONS="--collector.nfs --log.level='fatal'"

```
[Unit]
Description=Hello World 
After=multi-user.target

[Service]
Type=simple
EnvironmentFile=/etc/default/node_exporter
ExecStart=/usr/local/bin/scriptforexporter start $OPTIONS
ExecStop=/usr/local/bin/scriptforexporter stop 
TimeoutStopSec=infinity

[Install]
WantedBy=multi-user.target
```
Далее перезапускаю демон сервисов;Стартую сервис: node_exporter; Добавляю его в автозагрузку; Проверяю статус процесса.

```
root@Daemon:~# sudo systemctl daemon-reload
root@Daemon:~# sudo systemctl start node_exporter
root@Daemon:~# sudo systemctl enable node_exporter
root@Daemon:~# sudo systemctl status node_exporter
● node_exporter.service - Hello World
     Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2022-02-06 00:55:40 MSK; 12s ago
   Main PID: 6651 (scriptforexport)
      Tasks: 2 (limit: 14260)
     Memory: 544.0K
     CGroup: /system.slice/node_exporter.service
             ├─6651 /bin/bash /usr/local/bin/scriptforexporter start
             └─6684 sleep 10

фев 06 00:55:40 Daemon systemd[1]: Started Hello World.
фев 06 00:55:40 Daemon scriptforexporter[6651]: Hello World Вс 06 фев 2022 00:55:40 MSK
фев 06 00:55:50 Daemon scriptforexporter[6651]: Hello World Вс 06 фев 2022 00:55:50 MSK
```
После перезагрузки результат тот же, сервис работает, всё отлично.
#### 2.Ознакомьтесь с опциями node_exporter и выводом `/metrics` по-умолчанию. Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.
#### Ответ: Запускаю node_exporter.service
```
● node_exporter.service - Prometheus Node Exporter
     Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
     Active: active (running) since Mon 2022-02-07 02:35:53 MSK; 25s ago
   Main PID: 21169 (node_exporter)
      Tasks: 5 (limit: 14260)
     Memory: 2.5M
     CGroup: /system.slice/node_exporter.service
             └─21169 /usr/local/bin/node_exporter

фев 07 02:35:53 Daemon node_exporter[21169]: ts=2022-02-06T23:35:53.354Z caller=node_exporter.go:115 level=info collector=thermal_zone
фев 07 02:35:53 Daemon node_exporter[21169]: ts=2022-02-06T23:35:53.354Z caller=node_exporter.go:115 level=info collector=time
фев 07 02:35:53 Daemon node_exporter[21169]: ts=2022-02-06T23:35:53.354Z caller=node_exporter.go:115 level=info collector=timex
фев 07 02:35:53 Daemon node_exporter[21169]: ts=2022-02-06T23:35:53.354Z caller=node_exporter.go:115 level=info collector=udp_queues
фев 07 02:35:53 Daemon node_exporter[21169]: ts=2022-02-06T23:35:53.354Z caller=node_exporter.go:115 level=info collector=uname
фев 07 02:35:53 Daemon node_exporter[21169]: ts=2022-02-06T23:35:53.354Z caller=node_exporter.go:115 level=info collector=vmstat
фев 07 02:35:53 Daemon node_exporter[21169]: ts=2022-02-06T23:35:53.354Z caller=node_exporter.go:115 level=info collector=xfs
фев 07 02:35:53 Daemon node_exporter[21169]: ts=2022-02-06T23:35:53.354Z caller=node_exporter.go:115 level=info collector=zfs
фев 07 02:35:53 Daemon node_exporter[21169]: ts=2022-02-06T23:35:53.354Z caller=node_exporter.go:199 level=info msg="Listening on" address=:9100
фев 07 02:35:53 Daemon node_exporter[21169]: ts=2022-02-06T23:35:53.354Z caller=tls_config.go:195 level=info msg="TLS is disabled." http2=false
```
Далее вхожу через браузер к примеру, на свой IP-адрес, порт 9100, указанный сервисом при запуске, с ключом - /metrics (http://192.168.0.110:9100/metrics) и наблюдаем все интересующие нас характеристики:

```
# HELP node_cpu_seconds_total Seconds the CPUs spent in each mode.
# TYPE node_cpu_seconds_total counter
node_cpu_seconds_total{cpu="0",mode="idle"} 7141.84
node_cpu_seconds_total{cpu="0",mode="iowait"} 10.85
node_cpu_seconds_total{cpu="0",mode="irq"} 0
node_cpu_seconds_total{cpu="0",mode="nice"} 3.75
node_cpu_seconds_total{cpu="0",mode="softirq"} 0.39
node_cpu_seconds_total{cpu="0",mode="steal"} 0
node_cpu_seconds_total{cpu="0",mode="system"} 995.11
node_cpu_seconds_total{cpu="0",mode="user"} 3065.58
node_cpu_seconds_total{cpu="1",mode="idle"} 7203.8
node_cpu_seconds_total{cpu="1",mode="iowait"} 9.6
node_cpu_seconds_total{cpu="1",mode="irq"} 0
node_cpu_seconds_total{cpu="1",mode="nice"} 4.18
node_cpu_seconds_total{cpu="1",mode="softirq"} 0.19
node_cpu_seconds_total{cpu="1",mode="steal"} 0
node_cpu_seconds_total{cpu="1",mode="system"} 1027.5
node_cpu_seconds_total{cpu="1",mode="user"} 2954.45
node_cpu_seconds_total{cpu="2",mode="idle"} 6990.7
node_cpu_seconds_total{cpu="2",mode="iowait"} 11.16
node_cpu_seconds_total{cpu="2",mode="irq"} 0
node_cpu_seconds_total{cpu="2",mode="nice"} 2.5
node_cpu_seconds_total{cpu="2",mode="softirq"} 19.41
node_cpu_seconds_total{cpu="2",mode="steal"} 0
node_cpu_seconds_total{cpu="2",mode="system"} 989.23
node_cpu_seconds_total{cpu="2",mode="user"} 3194.83
node_cpu_seconds_total{cpu="3",mode="idle"} 6869.33
node_cpu_seconds_total{cpu="3",mode="iowait"} 15.75
node_cpu_seconds_total{cpu="3",mode="irq"} 0
node_cpu_seconds_total{cpu="3",mode="nice"} 2.79
node_cpu_seconds_total{cpu="3",mode="softirq"} 29.01
node_cpu_seconds_total{cpu="3",mode="steal"} 0
node_cpu_seconds_total{cpu="3",mode="system"} 987.75
node_cpu_seconds_total{cpu="3",mode="user"} 3322.57

# HELP node_disk_io_time_seconds_total Total seconds spent doing I/Os.
# TYPE node_disk_io_time_seconds_total counter
node_disk_io_time_seconds_total{device="sda"} 0.404
node_disk_io_time_seconds_total{device="sdb"} 0.496
node_disk_io_time_seconds_total{device="sdc"} 123.208
node_disk_io_time_seconds_total{device="sdd"} 0.432
# HELP node_disk_io_time_weighted_seconds_total The weighted # of seconds spent doing I/Os.
# TYPE node_disk_io_time_weighted_seconds_total counter
node_disk_io_time_weighted_seconds_total{device="sda"} 0.47700000000000004
node_disk_io_time_weighted_seconds_total{device="sdb"} 0.512
node_disk_io_time_weighted_seconds_total{device="sdc"} 124.24900000000001
node_disk_io_time_weighted_seconds_total{device="sdd"} 0.367
# HELP node_disk_read_bytes_total The total number of bytes read successfully.
# TYPE node_disk_read_bytes_total counter
node_disk_read_bytes_total{device="sda"} 8.9984e+06
node_disk_read_bytes_total{device="sdb"} 5.694464e+06
node_disk_read_bytes_total{device="sdc"} 1.610171904e+09
node_disk_read_bytes_total{device="sdd"} 5.643776e+06
# HELP node_disk_read_time_seconds_total The total number of seconds spent by all reads.
# TYPE node_disk_read_time_seconds_total counter
node_disk_read_time_seconds_total{device="sda"} 0.47700000000000004
node_disk_read_time_seconds_total{device="sdb"} 0.512
node_disk_read_time_seconds_total{device="sdc"} 14.569
node_disk_read_time_seconds_total{device="sdd"} 0.367

# HELP node_memory_MemAvailable_bytes Memory information field MemAvailable_bytes.
# TYPE node_memory_MemAvailable_bytes gauge
node_memory_MemAvailable_bytes 7.587385344e+09
# HELP node_memory_MemFree_bytes Memory information field MemFree_bytes.
# TYPE node_memory_MemFree_bytes gauge
node_memory_MemFree_bytes 4.543041536e+09
# HELP node_memory_MemTotal_bytes Memory information field MemTotal_bytes.
# TYPE node_memory_MemTotal_bytes gauge
node_memory_MemTotal_bytes 1.2535341056e+10

# HELP node_network_receive_bytes_total Network device statistic receive_bytes.
# TYPE node_network_receive_bytes_total counter
node_network_receive_bytes_total{device="enp3s0"} 1.301622755e+09
node_network_receive_bytes_total{device="lo"} 1.406939e+07
# HELP node_network_receive_compressed_total Network device statistic receive_compressed.
# TYPE node_network_receive_compressed_total counter
node_network_receive_compressed_total{device="enp3s0"} 0
node_network_receive_compressed_total{device="lo"} 0
# HELP node_network_receive_drop_total Network device statistic receive_drop.
# TYPE node_network_receive_drop_total counter
node_network_receive_drop_total{device="enp3s0"} 3
node_network_receive_drop_total{device="lo"} 0
# HELP node_network_receive_errs_total Network device statistic receive_errs.
# TYPE node_network_receive_errs_total counter
node_network_receive_errs_total{device="enp3s0"} 0
node_network_receive_errs_total{device="lo"} 0
```
#### 3. Установите в свою виртуальную машину [Netdata](https://github.com/netdata/netdata). Воспользуйтесь [готовыми пакетами](https://packagecloud.io/netdata/netdata/install) для установки (`sudo apt install -y netdata`). После успешной установки:
    * в конфигурационном файле `/etc/netdata/netdata.conf` в секции [web] замените значение с localhost на `bind to = 0.0.0.0`,
    * добавьте в Vagrantfile проброс порта Netdata на свой локальный компьютер и сделайте `vagrant reload`:

    config.vm.network "forwarded_port", guest: 19999, host: 19999

После успешной перезагрузки в браузере *на своем ПК* (не в виртуальной машине) вы должны суметь зайти на `localhost:19999`. Ознакомьтесь с метриками, которые по умолчанию собираются Netdata и с комментариями, которые даны к этим метрикам.
#### Ответ: Изначально установил Netdata на свою ПЭВМ, вместо виртуальной машины, поэтому при перезагрузке `vagrant reload`, виртуальная машина руганулась на то, что порт 19999 уже занят, поэтому поменял в конфигурационном файле Vagrant порт хоста на 9999 `config.vm.network "forwarded_port", guest: 19999, host: 9999`
![изображение](https://github.com/Daemon-Angel/devops-netology/blob/main/Home.Work%D0%97.4%20OS%20lecture%202./Vagrant-Netdata.png)
#### 4. Можно ли по выводу `dmesg` понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?
#### Ответ: Думаю, что по результату выполнения команды, все очевидно
```
vagrant@vagrant:~$ dmesg | grep -i virtual
[    0.000000] DMI: innotek GmbH VirtualBox/VirtualBox, BIOS VirtualBox 12/01/2006
[    0.003310] CPU MTRRs all blank - virtualized system.
[    0.094207] Booting paravirtualized kernel on KVM
[    3.975054] systemd[1]: Detected virtualization oracle.
```
#### 5. Как настроен sysctl `fs.nr_open` на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий лимит не позволит достичь такого числа (`ulimit --help`)?
#### Ответ:
```
vagrant@vagrant:~$ sysctl -n fs.nr_open
1048576
Лимит на кол-во открытых дескрипторов

ulimit -n	the maximum number of open file descriptors
```
Существует два типа ограничений: «жесткое ограничение» и «мягкое ограничение».

«Жесткое ограничение» для открытых файлов статически заданное значение, и может быть изменен
только «корневым» пользователем Linux;

«Мягкое ограничение» — это ограничение, которое может изменяться процессами динамически,
т. е. Во время выполнения, если процессу требуется больше открытых файлов, чем разрешено мягким пределом.
#### 6.Запустите любой долгоживущий процесс (не `ls`, который отработает мгновенно, а, например, `sleep 1h`) в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через `nsenter`. Для простоты работайте в данном задании под root (`sudo -i`). Под обычным пользователем требуются дополнительные опции (`--map-root-user`) и т.д.
#### Ответ: Запускаем sleep через unshare:
```
root@vagrant:~# ps -e | grep sleep
root@vagrant:~# unshare -f --pid --mount-proc sleep 1h
^Z
[1]+  Stopped                 unshare -f --pid --mount-proc sleep 1h
root@vagrant:~# ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root        1679  0.0  0.0   5480   520 pts/0    T    23:12   0:00 unshare -f --pid --mo
root        1680  0.0  0.0   5476   596 pts/0    S    23:12   0:00 sleep 1h
root        1681  0.0  0.3   8892  3432 pts/0    R+   23:13   0:00 ps aux
```
Заходим в процесс:
```
root@vagrant:~# nsenter -t 1680 -p -m
root@vagrant:/# ps
    PID TTY          TIME CMD
      1 pts/0    00:00:00 sleep
      2 pts/0    00:00:00 bash
     13 pts/0    00:00:00 ps
```
#### 7. Найдите информацию о том, что такое `:(){ :|:& };:`. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 (**это важно, поведение в других ОС не проверялось**). Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. Вызов `dmesg` расскажет, какой механизм помог автоматической стабилизации. Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?
#### Ответ:  Команда :(){ :|:& };:
В действительности эта команда является логической бомбой. Она оперирует определением функции с именем ‘:‘, которая вызывает сама себя дважды: один раз на переднем плане и один раз в фоне. Она продолжает своё выполнение снова и снова, пока система не зависнет.

Результат dmesg:

```
root@vagrant:~# dmesg | grep fork
[ 3807.818463] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-3.scope
```
Здесь мы можем видеть,что сработал механизм сgroups - это способ ограничить ресурсы внутри конкретной cgroup(контрольной группы процессов).
Если установить ulimit -u 30 - число процессов будет ограниченно 30 для пользоователя. 








