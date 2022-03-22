# Домашнее задание к занятию "3.9. Элементы безопасности информационных систем"
#### 1. Установите Bitwarden плагин для браузера. Зарегестрируйтесь и сохраните несколько паролей.
#### Ответ: ![изображение](https://github.com/Daemon-Angel/devops-netology/blob/main/Home.Work%D0%97.9%20Security/Bitwarden%20Plugin.png)
#### 2. Установите Google authenticator на мобильный телефон. Настройте вход в Bitwarden акаунт через Google authenticator OTP.
#### Ответ: ![изображение](https://github.com/Daemon-Angel/devops-netology/blob/main/Home.Work%D0%97.9%20Security/2-%D1%85%20%D1%8D%D1%82%D0%B0%D0%BF%D0%BD%D1%8B%D0%B9.png)
#### 3. Установите apache2, сгенерируйте самоподписанный сертификат, настройте тестовый сайт для работы по HTTPS.
#### Ответ: 
`user@Daemon:~$ sudo apt install apache2`
```
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt
Generating a RSA private key
......................................................................................+++++
..............................................................................................................................................+++++
writing new private key to '/etc/ssl/private/apache-selfsigned.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:RU
State or Province Name (full name) [Some-State]:Shahunya
Locality Name (eg, city) []:Shahunya
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Netology
Organizational Unit Name (eg, section) []:Netology
Common Name (e.g. server FQDN or YOUR name) []:Dmitriy
Email Address []:kda-81@mail.ru
```
```
user@Daemon:~$ sudo vi /etc/apache2/conf-available/ssl-params.conf
[sudo] пароль для user: 
SSLCipherSuite EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH
SSLProtocol All -SSLv2 -SSLv3 -TLSv1 -TLSv1.1
SSLHonorCipherOrder On
# Disable preloading HSTS for now.  You can use the commented out header line that includes
# the "preload" directive if you understand the implications.
# Header always set Strict-Transport-Security "max-age=63072000; includeSubDomains; preload"
Header always set X-Frame-Options DENY
Header always set X-Content-Type-Options nosniff
# Requires Apache >= 2.4
SSLCompression off
SSLUseStapling on
SSLStaplingCache "shmcb:logs/stapling-cache(150000)"
# Requires Apache >= 2.4.11
SSLSessionTickets Off
user@Daemon:~$ sudo cp /etc/apache2/sites-available/default-ssl.conf /etc/apache2/sites-available/default-ssl.conf.bak
user@Daemon:~$ sudo vi /etc/apache2/sites-available/default-ssl.conf
<IfModule mod_ssl.c>
        <VirtualHost _default_:443>
                ServerAdmin my_email@example.com
                ServerName localhost

                DocumentRoot /var/www/html

                ErrorLog ${APACHE_LOG_DIR}/error.log
                CustomLog ${APACHE_LOG_DIR}/access.log combined

                SSLEngine on

                SSLCertificateFile      /etc/ssl/certs/apache-selfsigned.crt
                SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key

                <FilesMatch "\.(cgi|shtml|phtml|php)$">
                                SSLOptions +StdEnvVars
                </FilesMatch>
                <Directory /usr/lib/cgi-bin>
                                SSLOptions +StdEnvVars
                </Directory>

        </VirtualHost>
</IfModule>
user@Daemon:~$ sudo vi /etc/apache2/sites-available/000-default.conf
<VirtualHost *:80>
        . . .

        Redirect "/" "https://localhost/"

        . . .
</VirtualHost>
user@Daemon:~$ sudo a2enmod ssl
user@Daemon:~$ sudo a2enmod headers
user@Daemon:~$ sudo a2ensite default-ssl
user@Daemon:~$ sudo a2enconf ssl-params
user@Daemon:~$ sudo apache2ctl configtest
user@Daemon:~$ sudo systemctl restart apache2
```
#### 4. Проверьте на TLS уязвимости произвольный сайт в интернете (кроме сайтов МВД, ФСБ, МинОбр, НацБанк, РосКосмос, РосАтом, РосНАНО и любых госкомпаний, объектов КИИ, ВПК ... и тому подобное).
#### Ответ:
```
user@Daemon:~$ sudo nmap -A 188.114.98.128
Starting Nmap 7.80 ( https://nmap.org ) at 2022-03-19 02:22 MSK
Nmap scan report for 188.114.98.128
Host is up (0.013s latency).
Not shown: 996 filtered ports
PORT     STATE SERVICE       VERSION
80/tcp   open  http          cloudflare
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.1 400 Bad Request
|     Date: Fri, 18 Mar 2022 23:22:27 GMT
|     Content-Type: text/html
|     Content-Length: 155
|     Connection: close
|     Server: cloudflare
|     CF-RAY: 6ee1b5e20e469d51-DME
|     <html>
|     <head><title>400 Bad Request</title></head>
|     <body>
|     <center><h1>400 Bad Request</h1></center>
|     <hr><center>cloudflare</center>
|     </body>
|     </html>
|   GetRequest: 
|     HTTP/1.1 400 Bad Request
|     Date: Fri, 18 Mar 2022 23:22:27 GMT
|     Content-Type: text/html
|     Content-Length: 155
|     Connection: close
|     Server: cloudflare
|     CF-RAY: 6ee1b5e13df21604-DME
|     <html>
|     <head><title>400 Bad Request</title></head>
|     <body>
|     <center><h1>400 Bad Request</h1></center>
|     <hr><center>cloudflare</center>
|     </body>
|     </html>
|   HTTPOptions: 
|     HTTP/1.1 400 Bad Request
|     Date: Fri, 18 Mar 2022 23:22:27 GMT
|     Content-Type: text/html
|     Content-Length: 155
|     Connection: close
|     Server: cloudflare
|     CF-RAY: 6ee1b5e17a869015-DME
|     <html>
|     <head><title>400 Bad Request</title></head>
|     <body>
|     <center><h1>400 Bad Request</h1></center>
|     <hr><center>cloudflare</center>
|     </body>
|     </html>
|   RPCCheck: 
|     HTTP/1.1 400 Bad Request
|     Server: cloudflare
|     Date: Fri, 18 Mar 2022 23:22:32 GMT
|     Content-Type: text/html
|     Content-Length: 155
|     Connection: close
|     CF-RAY: -
|     <html>
|     <head><title>400 Bad Request</title></head>
|     <body>
|     <center><h1>400 Bad Request</h1></center>
|     <hr><center>cloudflare</center>
|     </body>
|     </html>
|   RTSPRequest: 
|     <html>
|     <head><title>400 Bad Request</title></head>
|     <body>
|     <center><h1>400 Bad Request</h1></center>
|     <hr><center>cloudflare</center>
|     </body>
|     </html>
|   X11Probe: 
|     HTTP/1.1 400 Bad Request
|     Server: cloudflare
|     Date: Fri, 18 Mar 2022 23:22:27 GMT
|     Content-Type: text/html
|     Content-Length: 155
|     Connection: close
|     CF-RAY: -
|     <html>
|     <head><title>400 Bad Request</title></head>
|     <body>
|     <center><h1>400 Bad Request</h1></center>
|     <hr><center>cloudflare</center>
|     </body>
|_    </html>
|_http-server-header: cloudflare
|_http-title: Site doesn't have a title (text/plain; charset=UTF-8).
443/tcp  open  ssl/http      nginx
|_http-server-header: cloudflare
|_http-title: 400 The plain HTTP request was sent to HTTPS port
8080/tcp open  http-proxy    cloudflare
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.1 400 Bad Request
|     Date: Fri, 18 Mar 2022 23:22:27 GMT
|     Content-Type: text/html
|     Content-Length: 155
|     Connection: close
|     Server: cloudflare
|     CF-RAY: 6ee1b5e1dd9a9d63-DME
|     <html>
|     <head><title>400 Bad Request</title></head>
|     <body>
|     <center><h1>400 Bad Request</h1></center>
|     <hr><center>cloudflare</center>
|     </body>
|     </html>
|   GetRequest: 
|     HTTP/1.1 400 Bad Request
|     Date: Fri, 18 Mar 2022 23:22:27 GMT
|     Content-Type: text/html
|     Content-Length: 155
|     Connection: close
|     Server: cloudflare
|     CF-RAY: 6ee1b5e13dac005d-DME
|     <html>
|     <head><title>400 Bad Request</title></head>
|     <body>
|     <center><h1>400 Bad Request</h1></center>
|     <hr><center>cloudflare</center>
|     </body>
|     </html>
|   HTTPOptions: 
|     HTTP/1.1 400 Bad Request
|     Date: Fri, 18 Mar 2022 23:22:27 GMT
|     Content-Type: text/html
|     Content-Length: 155
|     Connection: close
|     Server: cloudflare
|     CF-RAY: 6ee1b5e17b3a7b4b-DME
|     <html>
|     <head><title>400 Bad Request</title></head>
|     <body>
|     <center><h1>400 Bad Request</h1></center>
|     <hr><center>cloudflare</center>
|     </body>
|     </html>
|   RTSPRequest: 
|     <html>
|     <head><title>400 Bad Request</title></head>
|     <body>
|     <center><h1>400 Bad Request</h1></center>
|     <hr><center>cloudflare</center>
|     </body>
|     </html>
|   Socks4, Socks5: 
|     HTTP/1.1 400 Bad Request
|     Server: cloudflare
|     Date: Fri, 18 Mar 2022 23:22:27 GMT
|     Content-Type: text/html
|     Content-Length: 155
|     Connection: close
|     CF-RAY: -
|     <html>
|     <head><title>400 Bad Request</title></head>
|     <body>
|     <center><h1>400 Bad Request</h1></center>
|     <hr><center>cloudflare</center>
|     </body>
|_    </html>
|_http-server-header: cloudflare
|_http-title: Site doesn't have a title (text/plain; charset=UTF-8).
8443/tcp open  ssl/https-alt cloudflare
|_http-server-header: cloudflare
|_http-title: 400 The plain HTTP request was sent to HTTPS port
2 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port80-TCP:V=7.80%I=7%D=3/19%Time=62351433%P=x86_64-pc-linux-gnu%r(GetR
SF:equest,14F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nDate:\x20Fri,\x2018\x
SF:20Mar\x202022\x2023:22:27\x20GMT\r\nContent-Type:\x20text/html\r\nConte
SF:nt-Length:\x20155\r\nConnection:\x20close\r\nServer:\x20cloudflare\r\nC
SF:F-RAY:\x206ee1b5e13df21604-DME\r\n\r\n<html>\r\n<head><title>400\x20Bad
SF:\x20Request</title></head>\r\n<body>\r\n<center><h1>400\x20Bad\x20Reque
SF:st</h1></center>\r\n<hr><center>cloudflare</center>\r\n</body>\r\n</htm
SF:l>\r\n")%r(HTTPOptions,14F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nDate:
SF:\x20Fri,\x2018\x20Mar\x202022\x2023:22:27\x20GMT\r\nContent-Type:\x20te
SF:xt/html\r\nContent-Length:\x20155\r\nConnection:\x20close\r\nServer:\x2
SF:0cloudflare\r\nCF-RAY:\x206ee1b5e17a869015-DME\r\n\r\n<html>\r\n<head><
SF:title>400\x20Bad\x20Request</title></head>\r\n<body>\r\n<center><h1>400
SF:\x20Bad\x20Request</h1></center>\r\n<hr><center>cloudflare</center>\r\n
SF:</body>\r\n</html>\r\n")%r(RTSPRequest,9B,"<html>\r\n<head><title>400\x
SF:20Bad\x20Request</title></head>\r\n<body>\r\n<center><h1>400\x20Bad\x20
SF:Request</h1></center>\r\n<hr><center>cloudflare</center>\r\n</body>\r\n
SF:</html>\r\n")%r(X11Probe,13C,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nSer
SF:ver:\x20cloudflare\r\nDate:\x20Fri,\x2018\x20Mar\x202022\x2023:22:27\x2
SF:0GMT\r\nContent-Type:\x20text/html\r\nContent-Length:\x20155\r\nConnect
SF:ion:\x20close\r\nCF-RAY:\x20-\r\n\r\n<html>\r\n<head><title>400\x20Bad\
SF:x20Request</title></head>\r\n<body>\r\n<center><h1>400\x20Bad\x20Reques
SF:t</h1></center>\r\n<hr><center>cloudflare</center>\r\n</body>\r\n</html
SF:>\r\n")%r(FourOhFourRequest,14F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\n
SF:Date:\x20Fri,\x2018\x20Mar\x202022\x2023:22:27\x20GMT\r\nContent-Type:\
SF:x20text/html\r\nContent-Length:\x20155\r\nConnection:\x20close\r\nServe
SF:r:\x20cloudflare\r\nCF-RAY:\x206ee1b5e20e469d51-DME\r\n\r\n<html>\r\n<h
SF:ead><title>400\x20Bad\x20Request</title></head>\r\n<body>\r\n<center><h
SF:1>400\x20Bad\x20Request</h1></center>\r\n<hr><center>cloudflare</center
SF:>\r\n</body>\r\n</html>\r\n")%r(RPCCheck,13C,"HTTP/1\.1\x20400\x20Bad\x
SF:20Request\r\nServer:\x20cloudflare\r\nDate:\x20Fri,\x2018\x20Mar\x20202
SF:2\x2023:22:32\x20GMT\r\nContent-Type:\x20text/html\r\nContent-Length:\x
SF:20155\r\nConnection:\x20close\r\nCF-RAY:\x20-\r\n\r\n<html>\r\n<head><t
SF:itle>400\x20Bad\x20Request</title></head>\r\n<body>\r\n<center><h1>400\
SF:x20Bad\x20Request</h1></center>\r\n<hr><center>cloudflare</center>\r\n<
SF:/body>\r\n</html>\r\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port8080-TCP:V=7.80%I=7%D=3/19%Time=62351433%P=x86_64-pc-linux-gnu%r(Ge
SF:tRequest,14F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nDate:\x20Fri,\x2018
SF:\x20Mar\x202022\x2023:22:27\x20GMT\r\nContent-Type:\x20text/html\r\nCon
SF:tent-Length:\x20155\r\nConnection:\x20close\r\nServer:\x20cloudflare\r\
SF:nCF-RAY:\x206ee1b5e13dac005d-DME\r\n\r\n<html>\r\n<head><title>400\x20B
SF:ad\x20Request</title></head>\r\n<body>\r\n<center><h1>400\x20Bad\x20Req
SF:uest</h1></center>\r\n<hr><center>cloudflare</center>\r\n</body>\r\n</h
SF:tml>\r\n")%r(HTTPOptions,14F,"HTTP/1\.1\x20400\x20Bad\x20Request\r\nDat
SF:e:\x20Fri,\x2018\x20Mar\x202022\x2023:22:27\x20GMT\r\nContent-Type:\x20
SF:text/html\r\nContent-Length:\x20155\r\nConnection:\x20close\r\nServer:\
SF:x20cloudflare\r\nCF-RAY:\x206ee1b5e17b3a7b4b-DME\r\n\r\n<html>\r\n<head
SF:><title>400\x20Bad\x20Request</title></head>\r\n<body>\r\n<center><h1>4
SF:00\x20Bad\x20Request</h1></center>\r\n<hr><center>cloudflare</center>\r
SF:\n</body>\r\n</html>\r\n")%r(RTSPRequest,9B,"<html>\r\n<head><title>400
SF:\x20Bad\x20Request</title></head>\r\n<body>\r\n<center><h1>400\x20Bad\x
SF:20Request</h1></center>\r\n<hr><center>cloudflare</center>\r\n</body>\r
SF:\n</html>\r\n")%r(FourOhFourRequest,14F,"HTTP/1\.1\x20400\x20Bad\x20Req
SF:uest\r\nDate:\x20Fri,\x2018\x20Mar\x202022\x2023:22:27\x20GMT\r\nConten
SF:t-Type:\x20text/html\r\nContent-Length:\x20155\r\nConnection:\x20close\
SF:r\nServer:\x20cloudflare\r\nCF-RAY:\x206ee1b5e1dd9a9d63-DME\r\n\r\n<htm
SF:l>\r\n<head><title>400\x20Bad\x20Request</title></head>\r\n<body>\r\n<c
SF:enter><h1>400\x20Bad\x20Request</h1></center>\r\n<hr><center>cloudflare
SF:</center>\r\n</body>\r\n</html>\r\n")%r(Socks5,13C,"HTTP/1\.1\x20400\x2
SF:0Bad\x20Request\r\nServer:\x20cloudflare\r\nDate:\x20Fri,\x2018\x20Mar\
SF:x202022\x2023:22:27\x20GMT\r\nContent-Type:\x20text/html\r\nContent-Len
SF:gth:\x20155\r\nConnection:\x20close\r\nCF-RAY:\x20-\r\n\r\n<html>\r\n<h
SF:ead><title>400\x20Bad\x20Request</title></head>\r\n<body>\r\n<center><h
SF:1>400\x20Bad\x20Request</h1></center>\r\n<hr><center>cloudflare</center
SF:>\r\n</body>\r\n</html>\r\n")%r(Socks4,13C,"HTTP/1\.1\x20400\x20Bad\x20
SF:Request\r\nServer:\x20cloudflare\r\nDate:\x20Fri,\x2018\x20Mar\x202022\
SF:x2023:22:27\x20GMT\r\nContent-Type:\x20text/html\r\nContent-Length:\x20
SF:155\r\nConnection:\x20close\r\nCF-RAY:\x20-\r\n\r\n<html>\r\n<head><tit
SF:le>400\x20Bad\x20Request</title></head>\r\n<body>\r\n<center><h1>400\x2
SF:0Bad\x20Request</h1></center>\r\n<hr><center>cloudflare</center>\r\n</b
SF:ody>\r\n</html>\r\n");
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Android 6.0 - 7.1.2 (Linux 3.18 - 4.4.1) (87%), Android 7.1.2 (Linux 3.4) (87%), HIKVISION DS-7600 Linux Embedded NVR (Linux 2.6.10) (87%), Vodavi XTS-IP PBX (86%), Android 7.0 (Linux 3.18) (86%), Apple iOS 11.0 (86%), FreeBSD 11.0-RELEASE (86%), Linux 3.0 (86%), DD-WRT v24-sp2 (Linux 3.10) (86%), Apple TV 5 (85%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 7 hops

TRACEROUTE (using port 80/tcp)
HOP RTT      ADDRESS
1   0.67 ms  _gateway (192.168.0.1)
2   5.68 ms  81.177.115.149
3   ... 5
6   14.58 ms 95.71.2.226
7   13.41 ms 188.114.98.128

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 207.54 seconds
```
#### 5. Установите на Ubuntu ssh сервер, сгенерируйте новый приватный ключ. Скопируйте свой публичный ключ на другой сервер. Подключитесь к серверу по SSH-ключу.
#### Ответ:
```
vagrant@VM1:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/vagrant/.ssh/id_rsa):
....
vagrant@VM1:~$ ssh-copy-id -i .ssh/id_rsa vagrant@192.168.0.110
....
vagrant@VM1:~$ ssh vagrant@192.168.0.110
Welcome to Ubuntu 20.04.1 LTS (GNU/Linux 5.13.0-35-generic x86_64)
....
vagrant@VM2:~$
```
#### 6. Переименуйте файлы ключей из задания 5. Настройте файл конфигурации SSH клиента, так чтобы вход на удаленный сервер осуществлялся по имени сервера.
#### Ответ:
```
vagrant@VM1:~$ sudo mv ~/.ssh/id_rsa ~/.ssh/id_rsa_netology
vagrant@VM1:~$ sudo vi ~/.ssh/config
Host VM2
        HostName 192.168.0.110
        User vagrant
        Port 22
        IdentityFile ~/.ssh/id_rsa_netology
vagrant@VM1:~$ ssh VM2
Welcome to Ubuntu 20.04.1 LTS (GNU/Linux 5.13.0-35-generic x86_64)
....
vagrant@VM2:~$
```
#### 7. Соберите дамп трафика утилитой tcpdump в формате pcap, 100 пакетов. Откройте файл pcap в Wireshark.
#### Ответ:
```
root@Daemon:/home/user# tcpdump -nnei any -c 100 -w 100.pcap
tcpdump: listening on any, link-type LINUX_SLL (Linux cooked v1), capture size 262144 bytes
100 packets captured
105 packets received by filter
0 packets dropped by kernel

```
![изображение](https://github.com/Daemon-Angel/devops-netology/blob/main/Home.Work%D0%97.9%20Security/100.png)


