# Домашнее задание к занятию "3.7. Компьютерные сети, лекция 2"
#### 1. Проверьте список доступных сетевых интерфейсов на вашем компьютере. Какие команды есть для этого в Linux и в Windows?
#### Ответ:
Для Linux:
```
user@Daemon:~$ ip link show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enp3s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP mode DEFAULT group default qlen 1000
    link/ether 00:25:22:c3:67:fd brd ff:ff:ff:ff:ff:ff
user@Daemon:~$ ifconfig -a
enp3s0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.0.110  netmask 255.255.255.0  broadcast 192.168.0.255
        inet6 fe80::c276:6efa:16fe:7c00  prefixlen 64  scopeid 0x20<link>
        ether 00:25:22:c3:67:fd  txqueuelen 1000  (Ethernet)
        RX packets 398643  bytes 543149830 (543.1 MB)
        RX errors 0  dropped 1  overruns 0  frame 0
        TX packets 195181  bytes 27504733 (27.5 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Локальная петля (Loopback))
        RX packets 7045  bytes 627600 (627.6 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 7045  bytes 627600 (627.6 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
``` 
Для Windows:
`ipconfig /all`
#### 2. Какой протокол используется для распознавания соседа по сетевому интерфейсу? Какой пакет и команды есть в Linux для этого?
#### Ответ:

Протокол: LLDP.

Пакет: lldpd.

Команда: lldpctl; lldpcli sh neigh
#### 3. Какая технология используется для разделения L2 коммутатора на несколько виртуальных сетей? Какой пакет и команды есть в Linux для этого? Приведите пример конфига.
#### Ответ:
Технология называется - VLAN (Virtual LAN).
Пакет в Ubuntu Linux называется - vlan
Пример конфига:
```
network:
  version: 2
  renderer: networkd
  ethernets:
    ens4:
      optional: yes
      addresses: 
        - 192.168.0.2/24
  vlans:
    vlan555:
      id: 555
      link: ens4 
      addresses:
        - 192.168.1.2/24
```



