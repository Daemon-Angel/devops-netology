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


