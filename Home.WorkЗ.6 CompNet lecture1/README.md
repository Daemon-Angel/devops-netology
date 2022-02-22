# Домашнее задание к занятию "3.6. Компьютерные сети, лекция 1"
#### 1. Работа c HTTP через телнет.
- Подключитесь утилитой телнет к сайту stackoverflow.com `telnet stackoverflow.com 80`
- отправьте HTTP запрос
```
GET /questions HTTP/1.0
HOST: stackoverflow.com
[press enter]
[press enter]
```
- В ответе укажите полученный HTTP код, что он означает?
#### Ответ: 
```
user@Daemon:~$ telnet stackoverflow.com 80
Trying 151.101.65.69...
Connected to stackoverflow.com.
Escape character is '^]'.
GET /questions HTTP/1.0
HOST: stackoverflow.com

HTTP/1.1 301 Moved Permanently
cache-control: no-cache, no-store, must-revalidate
location: https://stackoverflow.com/questions
x-request-guid: 3039183d-3933-498a-bad2-2455f071d460
feature-policy: microphone 'none'; speaker 'none'
content-security-policy: upgrade-insecure-requests; frame-ancestors 'self' https://stackexchange.com
Accept-Ranges: bytes
Date: Sun, 20 Feb 2022 13:22:31 GMT
Via: 1.1 varnish
Connection: close
X-Served-By: cache-fra19183-FRA
X-Cache: MISS
X-Cache-Hits: 0
X-Timer: S1645363351.131764,VS0,VE85
Vary: Fastly-SSL
X-DNS-Prefetch-Control: off
Set-Cookie: prov=fe0274e2-dc96-852c-6bce-ee8ec74cc3ae; domain=.stackoverflow.com; expires=Fri, 01-Jan-2055 00:00:00 GMT; path=/; HttpOnly

Connection closed by foreign host.
```
В ответ получили код 301 - редирект с HTTP на HTTPS протокол того же url
#### 2. Повторите задание 1 в браузере, используя консоль разработчика F12.
- откройте вкладку Network
- отправьте запрос http://stackoverflow.com
- найдите первый ответ HTTP сервера, откройте вкладку `Headers`
- укажите в ответе полученный HTTP код.
- проверьте время загрузки страницы, какой запрос обрабатывался дольше всего?
- приложите скриншот консоли браузера в ответ.
#### Ответ:
- укажите в ответе полученный HTTP код.

301, редирект
![изображение](https://github.com/Daemon-Angel/devops-netology/blob/main/Home.Work%D0%97.6%20CompNet%20lecture1/stackoverflow2-2.jpg)
- проверьте время загрузки страницы, какой запрос обрабатывался дольше всего?
- приложите скриншот консоли браузера в ответ.

Страница загрузилась за 2.45с, самый долгий запрос - stackoverflow.com, 643мс, а также несколько script за 200 мс.
![изображение](https://github.com/Daemon-Angel/devops-netology/blob/main/Home.Work%D0%97.6%20CompNet%20lecture1/stackoverflow3-3.jpg)
#### 3. Какой IP адрес у вас в интернете?
#### Ответ:
```
user@Daemon:~$ dig @resolver4.opendns.com myip.opendns.com +short
89.109.50.246
```
#### 4. Какому провайдеру принадлежит ваш IP адрес? Какой автономной системе AS? Воспользуйтесь утилитой `whois`
#### Ответ:
```
user@Daemon:~$ whois 89.109.50.246 | grep ^descr
descr:          Network for PPPoE clients terminations in
descr:          N.Novgorod city
descr:          Rostelecom networks
descr:          NMTS Autonomous System
```
Адрес пренадлежит оператору "Ростелеком"
```
user@Daemon:~$ whois 89.109.50.246 | grep ^origin
origin:         AS12389
origin:         AS25405
```
Номер AS: AS12389; AS25405
#### 5. Через какие сети проходит пакет, отправленный с вашего компьютера на адрес 8.8.8.8? Через какие AS? Воспользуйтесь утилитой `traceroute`
#### Ответ:
```
user@Daemon:~$ traceroute -An 8.8.8.8 
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  192.168.0.1 [*]  0.491 ms  0.458 ms  0.433 ms
 2  81.177.115.149 [AS12389]  6.043 ms  6.042 ms  6.032 ms
 3  81.177.115.148 [AS12389]  4.957 ms  4.946 ms  4.916 ms
 4  109.172.24.143 [AS12389]  5.101 ms  5.094 ms  4.944 ms
 5  * * *
 7  108.170.250.34 [AS15169]  12.394 ms 108.170.250.66 [AS15169]  14.722 ms *
 8  209.85.255.136 [AS15169]  28.370 ms * *
 9  172.253.65.82 [AS15169]  24.876 ms 108.170.235.204 [AS15169]  29.338 ms 72.14.238.168 [AS15169]  35.478 ms
10  142.250.209.171 [AS15169]  28.746 ms 209.85.251.41 [AS15169]  30.326 ms 216.239.57.229 [AS15169]  28.410 ms
11  * * *

21  * * 8.8.8.8 [AS15169]  25.941 ms
```
Пакет проходит через следующие AS: 12389; 15169
```
user@Daemon:~$ grep org-name <(whois AS12389)
org-name:       PJSC Rostelecom
user@Daemon:~$ grep OrgName <(whois AS15169)
OrgName:        Google LLC
```







