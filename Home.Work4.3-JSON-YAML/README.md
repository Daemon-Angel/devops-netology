#Домашнее задание к занятию "4.3. Языки разметки JSON и YAML"
##Обязательные задания
1.   Мы выгрузили JSON, который получили через API запрос к нашему сервису:

    { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : 7175 
            },
            { "name" : "second",
            "type" : "proxy",
            "ip" : "71.78.22.43"
            }
        ]
    }

Нужно найти и исправить все ошибки, которые допускает наш сервис

##Ответ: Были пропущены кавычки

2.    В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP. К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML файлов, описывающих наши сервисы. Формат записи JSON по одному сервису: { "имя сервиса" : "его IP"}. Формат записи YAML по одному сервису: - имя сервиса: его IP. Если в момент исполнения скрипта меняется IP у сервиса - он должен так же поменяться в yml и json файле.

##Ответ:
```
#!/usr/bin/env python3

import socket
import json
import yaml


file_old = open('ip_add.log', 'r')
stage_ipadds = json.loads(file_old.read())

ip_item = []
dns_item = ["drive.google.com", "mail.google.com", "google.com"]
for resolv in dns_item:
    ip_item.append(socket.gethostbyname(resolv))
current_ipadds = dict(zip(dns_item, ip_item))

stage_ipadds_new = open('ip_add.log', 'w')
stage_ipadds_new.write(json.dumps(current_ipadds))
stage_ipadds_new.close()

print('For check:')
print(current_ipadds)
print(f'{stage_ipadds}\n')

with open('first.json', 'w') as js:
    js.write('')
with open('second.yml', 'w') as ym:
    ym.write('')

for i in current_ipadds:
    if (current_ipadds[i] == stage_ipadds[i]):
        print(f'<{i}> - <{current_ipadds[i]}>')
        rr = {i: current_ipadds[i]}
        with open('first.json', 'a') as js:
            js.write(f'{json.dumps(rr)}\n')
        with open('second.yml', 'a') as ym:
            ym.write(yaml.dump(rr))
    else:
        print(f'[ERROR] <{i}> IP mismatch: <{stage_ipadds[i]}> <{current_ipadds[i]}>')
        rr2 = {i: current_ipadds[i]}
        with open('first.json', 'a') as js:
            js.write(f'{json.dumps(rr2)}\n')
        with open('second.yml', 'a') as ym:
            ym.write(yaml.dump(rr2))
 ```
