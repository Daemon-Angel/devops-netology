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


