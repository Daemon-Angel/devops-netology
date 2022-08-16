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
user@Daemon:~$ mkdir html
user@Daemon:~$ cd html
user@Daemon:~/html$ touch index.html
user@Daemon:~/html$ touch Dockerfile
user@Daemon:~/html$ code .
# Далее в Visual Studio Code внес необходимую нам информацию в файл index.html и в 
Dockerfile : FROM nginx:alpine
             COPY . /usr/share/nginx/html/test
user@Daemon:~/html$ docker build -t html .
Sending build context to Docker daemon  3.072kB
Step 1/2 : FROM nginx:alpine
alpine: Pulling from library/nginx
213ec9aee27d: Pull complete 
2546ae67167b: Pull complete 
23b845224e13: Pull complete 
9bd5732789a3: Pull complete 
328309e59ded: Pull complete 
b231d02e5150: Pull complete 
Digest: sha256:082f8c10bd47b6acc8ef15ae61ae45dd8fde0e9f389a8b5cb23c37408642bf5d
Status: Downloaded newer image for nginx:alpine
 ---> 804f9cebfdc5
Step 2/2 : COPY . /usr/share/nginx/html/test
 ---> 7fde872a375b
Successfully built 7fde872a375b
Successfully tagged html:latest
user@Daemon:~/html$ docker images
REPOSITORY                TAG       IMAGE ID       CREATED              SIZE
html                      latest    7fde872a375b   About a minute ago   23.5MB
user@Daemon:~/html$ docker run -p 8080:80 -d html
6c41a5bae7d6f28704f6c1865e348b019c16f7f2a4b2ddc867465ac0016ad351
user@Daemon:~/html$ docker ps
CONTAINER ID   IMAGE                            COMMAND                  CREATED          STATUS                    PORTS                                   NAMES
6c41a5bae7d6   html                             "/docker-entrypoint.…"   21 seconds ago   Up 20 seconds             0.0.0.0:8080->80/tcp, :::8080->80/tcp   unruffled_taussig
user@Daemon:~/html$ docker commit unruffled_taussig netology2
sha256:303825fb32af2108375bd63b187474ce6d05e04c1e5175652d9cd8baa95a56b6
user@Daemon:~/html$ docker images
REPOSITORY                TAG       IMAGE ID       CREATED          SIZE
netology2                 latest    303825fb32af   11 seconds ago   23.5MB
user@Daemon:~/html$ docker tag netology2 daemonangel/netology2
user@Daemon:~/html$ docker images
REPOSITORY                TAG       IMAGE ID       CREATED          SIZE
netology2                 latest    303825fb32af   6 minutes ago    23.5MB
daemonangel/netology2     latest    303825fb32af   6 minutes ago    23.5MB
user@Daemon:~/html$ docker push daemonangel/netology2
Using default tag: latest
The push refers to repository [docker.io/daemonangel/netology2]
b3f3d5e6c8fa: Pushed 
e308c8163f11: Pushed 
bf4e176a4d9b: Pushed 
a1d571e4e83d: Pushed 
6d97b4d00719: Pushed 
2a7647ca3937: Pushed 
549c42eea4a6: Pushed 
994393dc58e7: Pushed 
latest: digest: sha256:012d2a48fa37cccd578c908fbe5035a63315b2b0bdffd79435ed1bee772a1946 size: 1982
```
https://hub.docker.com/r/daemonangel/netology2
![изображение](https://github.com/Daemon-Angel/devops-netology/blob/main/05-virt-03-dockercont/%D0%A1%D0%BD%D0%B8%D0%BC%D0%BE%D0%BA%20%D1%8D%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%20%D0%BE%D1%82%202022-08-16%2001-36-09.png)
### Задача 2
Посмотрите на сценарий ниже и ответьте на вопрос: "Подходит ли в этом сценарии использование Docker контейнеров или лучше подойдет виртуальная машина, физическая машина? Может быть возможны разные варианты?"

Детально опишите и обоснуйте свой выбор.

--

Сценарий:

    Высоконагруженное монолитное java веб-приложение;
    Nodejs веб-приложение;
    Мобильное приложение c версиями для Android и iOS;
    Шина данных на базе Apache Kafka;
    Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana;
    Мониторинг-стек на базе Prometheus и Grafana;
    MongoDB, как основное хранилище данных для java-приложения;
    Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry.

### Ответ:


    Высоконагруженное монолитное java веб-приложение;
Физическая или виртуальная машина с выделенными ресурсами.

    Nodejs веб-приложение;
Docker подойдёт хорошо, т.к. это позволит быстро развернуть приложение со всеми необходимыми библиотеками.

    Мобильное приложение c версиями для Android и iOS;
Скорей всего нет, т.к. я не нашел информации о какой-то интеграции инструментов разработки под мобильные платформы с Docker.
В случае с Андроид - это apk-файл, в iOS свой файл.

    Шина данных на базе Apache Kafka;
Да, вполне. Доставка приложения через докер на сервера и разработчикам в тестовую среду.
Ещё очень важно иметь возможность быстро откатиться если приложение обновили, и в продакшене что-то пошло не так. Docker будет особенно удобен чтобы "вернуть как было".

    Elasticsearch кластер для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana;
Docker подойдёт лучше, так как он будет удобней для кластеризации: у контейнеров меньше оверхед.

    Мониторинг-стек на базе Prometheus и Grafana;
Docker подойдёт для этой задачи хорошо. Разворвачивать node_exporter с Docker скорей всего не стоит, т.к. ему требуется прямой доступ к метрикам ядра, но Prometheus и Grafana можно использовать в Докере.

    MongoDB, как основное хранилище данных для java-приложения;
Да, вполне подойдёт Docker. У MongoDB даже есть официальный образ на Docker Hub.

    Gitlab сервер для реализации CI/CD процессов и приватный (закрытый) Docker Registry.
В общем случае, думаю удобней будет виртуальная машина, т.к. серверу GitLab не требуется масштабирование или деплой новой версии несколько раз в день, а виртуальная машина позволит очень удобно делать бекапы и при необходимости мигрировать её в кластере, но думаю возможно и использование Docker.

### Задача 3

    Запустите первый контейнер из образа centos c любым тэгом в фоновом режиме, подключив папку /data из текущей рабочей директории на хостовой машине в /data контейнера;
    Запустите второй контейнер из образа debian в фоновом режиме, подключив папку /data из текущей рабочей директории на хостовой машине в /data контейнера;
    Подключитесь к первому контейнеру с помощью docker exec и создайте текстовый файл любого содержания в /data;
    Добавьте еще один файл в папку /data на хостовой машине;
    Подключитесь во второй контейнер и отобразите листинг и содержание файлов в /data контейнера.
    
### Ответ:
Запуск контейнеров:
```
user@Daemon:~$ docker run -it --rm -d --name centos -v $(pwd)/data:/data centos:latest
Unable to find image 'centos:latest' locally
latest: Pulling from library/centos
a1d0c7532777: Pull complete 
Digest: sha256:a27fd8080b517143cbbbab9dfb7c8571c40d67d534bbdee55bd6c473f432b177
Status: Downloaded newer image for centos:latest
ad3b81cad13feec7663ed0d7b47f4dcec495c60566a17c72cd74a40a50ef7655
user@Daemon:~$ docker run -it --rm -d --name debian -v $(pwd)/data:/data debian:stable
Unable to find image 'debian:stable' locally
stable: Pulling from library/debian
464fd0c84e4d: Pull complete 
Digest: sha256:b9b1f4a7df16fcf7f287802d827b80f481a1e4caecb62c40c2e26fdebdc2eab9
Status: Downloaded newer image for debian:stable
0de0694c9a90ee1e928e95d5d84de0a818ecd27e1ad625f4e51dea15e6d2ec29
```
Файл из контейнера с CentOS:
```
user@Daemon:~$ docker exec -it centos bash
[root@ad3b81cad13f /]# echo "Hi Netology! This is CentOS!" > /data/centos.txt
[root@ad3b81cad13f /]# exit
exit
```
Файл с хоста:
```
root@Daemon:/home/user# echo "Hi Netology! This is Host!" > data/host.txt
```
Файлы в директории /data в контейнере с Debian:
```
user@Daemon:~$ docker exec -it debian bash
root@0de0694c9a90:/# ls -l /data/
total 20
-rw-r--r-- 1 root root    29 Aug 16 19:22 centos.txt
-rw-r--r-- 1 root root    27 Aug 16 19:25 host.txt
```


