# Домашнее задание к занятию "3.9. Элементы безопасности информационных систем"
#### 1. Установите Bitwarden плагин для браузера. Зарегестрируйтесь и сохраните несколько паролей.
#### Ответ: ![изображение](https://github.com/Daemon-Angel/devops-netology/blob/main/Home.Work%D0%97.9%20Security/Bitwarden%20Plugin.png)
#### 2. Установите Google authenticator на мобильный телефон. Настройте вход в Bitwarden акаунт через Google authenticator OTP.
#### Ответ: ![изображение](https://github.com/Daemon-Angel/devops-netology/blob/main/Home.Work%D0%97.9%20Security/2-%D1%85%20%D1%8D%D1%82%D0%B0%D0%BF%D0%BD%D1%8B%D0%B9.png)
#### 3. Установите apache2, сгенерируйте самоподписанный сертификат, настройте тестовый сайт для работы по HTTPS.
#### Ответ: 
```user@Daemon:~$ sudo apt install apache2
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt
Generating a RSA private key
......................................................................................+++++
..............................................................................................................................................+++++
writing new private key to '/etc/ssl/private/apache-selfsigned.key'
-----
```


