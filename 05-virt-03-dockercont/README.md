## Домашнее задание к занятию "5.3. Введение. Экосистема. Архитектура. Жизненный цикл Docker контейнера"
### Задача 1
> Сценарий выполения задачи:

   - создайте свой репозиторий на https://hub.docker.com;
   - выберете любой образ, который содержит веб-сервер Nginx;
   - создайте свой fork образа;
   - реализуйте функциональность: запуск веб-сервера в фоне с индекс-страницей, содержащей HTML-код ниже:
    
```
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m DevOps Engineer!</h1>
</body>
</html>
```
   - Опубликуйте созданный форк в своем репозитории и предоставьте ответ в виде ссылки на https://hub.docker.com/username_repo.
### Ответ:
```
user@Daemon:~$ sudo docker login
Authenticating with existing credentials...
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
user@Daemon:~$ sudo docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
461246efe0a7: Pull complete 
a96aaf9a9ec3: Pull complete 
650d8b758441: Pull complete 
b138da793ac8: Pull complete 
bb1705539683: Pull complete 
b9ed43dcc388: Pull complete 
Digest: sha256:db345982a2f2a4257c6f699a499feb1d79451a1305e8022f16456ddc3ad6b94c
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
user@Daemon:~$ sudo mkdir -p /home/webuser/myproject/www
user@Daemon:~$ sudo mkdir -p /home/webuser/myproject/nginx_logs
user@Daemon:~$ sudo su
root@Daemon:/home/user# echo '<html><head>Hey, Netology<head><body><h1>I`m DevOps Engineer!</h1></body></html>' > /home/webuser/myproject/www/index.html
root@Daemon:/home/user# docker run --name nginx_myproject -p 8080:80 -v /home/webuser/myproject/www:/usr/share/nginx/html -v /home/webuser/myproject/nginx_logs:/var/log/nginx -d nginx
4b7c4ea492d2c3756ac80b4b93b8f67e88c35739cdd5efb8aefd7c2b3bbe5b9a
root@Daemon:/home/user# docker ps
CONTAINER ID   IMAGE                            COMMAND                  CREATED          STATUS                    PORTS                                   NAMES
4b7c4ea492d2   nginx                            "/docker-entrypoint.…"   19 seconds ago   Up 18 seconds             0.0.0.0:8080->80/tcp, :::8080->80/tcp   nginx_myproject
user@Daemon:~$ sudo docker commit nginx_myproject netology
sha256:3adb01ded8a2d3c08b8a8f5b5d21321fe1f251cfcf0e67fe351019152ba11934
user@Daemon:~$ sudo docker images
REPOSITORY                TAG       IMAGE ID       CREATED          SIZE
netology                  latest    3adb01ded8a2   35 seconds ago   142MB
nginx                     latest    41b0e86104ba   6 days ago       142MB
user@Daemon:~$ sudo docker tag netology daemonangel/netology1
user@Daemon:~$ sudo docker images
REPOSITORY                TAG       IMAGE ID       CREATED          SIZE
daemonangel/netology1     latest    3adb01ded8a2   11 minutes ago   142MB
netology                  latest    3adb01ded8a2   11 minutes ago   142MB
user@Daemon:~$ sudo docker push daemonangel/netology1
Using default tag: latest
The push refers to repository [docker.io/daemonangel/netology1]
e84d35a9094a: Pushed 
de100bd247e0: Pushed 
1d561d938628: Pushed 
c03189a5ef70: Pushed 
305b0db3a210: Pushed 
1c99a7efe9d9: Pushed 
43b3c4e3001c: Pushed 
latest: digest: sha256:d6fb58cab6e18742bc283a481d5ff86e1a70735073cfe45420cc8c2dc512e0b4 size: 1777
```
https://hub.docker.com/layers/daemonangel/netology1/latest/images/sha256-d6fb58cab6e18742bc283a481d5ff86e1a70735073cfe45420cc8c2dc512e0b4?context=explore

![изображение](https://github.com/Daemon-Angel/devops-netology/blob/main/Hom.png)


