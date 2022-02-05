# Домашнее задание к занятию "3.4. Операционные системы, лекция 2"

#### 1.На лекции мы познакомились с [node_exporter](https://github.com/prometheus/node_exporter/releases). В демонстрации его исполняемый файл запускался в background. Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. Используя знания из лекции по systemd, создайте самостоятельно простой [unit-файл](https://www.freedesktop.org/software/systemd/man/systemd.service.html) для node_exporter:

  * поместите его в автозагрузку,
  * предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на `systemctl cat cron`),
  * удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.
#### Ответ:

Установил node_exporter-1.3.1; 
Создаю небольшой скрипт (scriptforexporter), помещаю его в /usr/local/bin и делаем его исполняемым (sudo chmod +x /usr/local/bin/scriptforexporter)
Cоздаю unit file (node_exporter.service) для нового сервиса с таким содержимым:
```
[Unit]
Description=Hello World 
After=multi-user.target

[Service]
Type=simple
ExecStart=/usr/local/bin/scriptforexporter start
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




