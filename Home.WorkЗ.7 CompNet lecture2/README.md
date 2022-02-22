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
#### 4. Какие типы агрегации интерфейсов есть в Linux? Какие опции есть для балансировки нагрузки? Приведите пример конфига.
#### Ответ:
В Linux есть две технологии агрегации (LAG): bonding и teaming.  

Типы агрегации bonding:
```
user@Daemon:~$ modinfo bonding | grep mode:
parm:           mode:Mode of operation; 0 for balance-rr, 1 for active-backup, 2 for balance-xor, 3 for broadcast, 4 for 802.3ad, 5 for balance-tlb, 6 for balance-alb (charp)
```
`active-backup` и `broadcast` обеспечивают только отказоустойчивость  
`balance-tlb`, `balance-alb`, `balance-rr`, `balance-xor` и `802.3ad` обеспечат отказоустойчивость и балансировку

`balance-rr` - Политика round-robin. Пакеты отправляются последовательно, начиная с первого доступного интерфейса и заканчивая последним. Эта политика применяется для балансировки нагрузки и отказоустойчивости.  
`active-backup` - Политика активный-резервный. Только один сетевой интерфейс из объединённых будет активным. Другой интерфейс может стать активным, только в том случае, когда упадёт текущий активный интерфейс. Эта политика применяется для отказоустойчивости.  
`balance-xor` - Политика XOR. Передача распределяется между сетевыми картами используя формулу: [( «MAC адрес источника» XOR «MAC адрес назначения») по модулю «число интерфейсов»]. Получается одна и та же сетевая карта передаёт пакеты одним и тем же получателям. Политика XOR применяется для балансировки нагрузки и отказоустойчивости.  
`broadcast` - Широковещательная политика. Передает всё на все сетевые интерфейсы. Эта политика применяется для отказоустойчивости.  
`802.3ad` - Политика агрегирования каналов по стандарту IEEE 802.3ad. Создаются агрегированные группы сетевых карт с одинаковой скоростью и дуплексом. При таком объединении передача задействует все каналы в активной агрегации, согласно стандарту IEEE 802.3ad. Выбор через какой интерфейс отправлять пакет определяется политикой по умолчанию XOR политика.  
`balance-tlb` - Политика адаптивной балансировки нагрузки передачи. Исходящий трафик распределяется в зависимости от загруженности каждой сетевой карты (определяется скоростью загрузки). Не требует дополнительной настройки на коммутаторе. Входящий трафик приходит на текущую сетевую карту. Если она выходит из строя, то другая сетевая карта берёт себе MAC адрес вышедшей из строя карты.  
`balance-alb` - Политика адаптивной балансировки нагрузки. Включает в себя политику balance-tlb плюс осуществляет балансировку входящего трафика. Не требует дополнительной настройки на коммутаторе. Балансировка входящего трафика достигается путём ARP переговоров.  

`active-backup` на отказоустойчивость:
```
 network:
   version: 2
   renderer: networkd
   ethernets:
     ens3:
       dhcp4: no 
       optional: true
     ens5: 
       dhcp4: no 
       optional: true
   bonds:
     bond0: 
       dhcp4: yes 
       interfaces:
         - ens3
         - ens5
       parameters:
         mode: active-backup
         primary: ens3
         mii-monitor-interval: 2
```
`balance-alb` - балансировка:
```shell
   bonds:
     bond0: 
       dhcp4: yes 
       interfaces:
         - ens3
         - ens5
       parameters:
         mode: balance-alb
         mii-monitor-interval: 2
```
#### 5. Сколько IP адресов в сети с маской /29 ? Сколько /29 подсетей можно получить из сети с маской /24. Приведите несколько примеров /29 подсетей внутри сети 10.10.10.0/24.
#### Ответ:
```
user@Daemon:~$ ipcalc -b 10.10.10.0/29
Address:   10.10.10.0           
Netmask:   255.255.255.248 = 29 
Wildcard:  0.0.0.7              
=>
Network:   10.10.10.0/29        
HostMin:   10.10.10.1           
HostMax:   10.10.10.6           
Broadcast: 10.10.10.7           
Hosts/Net: 6                     Class A, Private Internet
```
Всего 8 ip-адресов, из которых: 1 адрес - для сети; 1 - широковещательный адрес и 6 адресов для хостов.
```
user@Daemon:~$ ipcalc -b 10.10.10.0/24
Address:   10.10.10.0           
Netmask:   255.255.255.0 = 24   
Wildcard:  0.0.0.255            
=>
Network:   10.10.10.0/24        
HostMin:   10.10.10.1           
HostMax:   10.10.10.254         
Broadcast: 10.10.10.255         
Hosts/Net: 254                   Class A, Private Internet
```
Сеть с маской /24 имеет 256 ip-адресов, соответственно 256/8=32,(8 ip-адресов имеет сеть с маской /29), получаем что данную сеть можно разбить на 32 подсети с маской /29.
Пример сетей с маской /29:
```
user@Daemon:~$ ipcalc -b --split 6 6 6 10.10.10.0/24
Address:   10.10.10.0           
Netmask:   255.255.255.0 = 24   
Wildcard:  0.0.0.255            
=>
Network:   10.10.10.0/24        
HostMin:   10.10.10.1           
HostMax:   10.10.10.254         
Broadcast: 10.10.10.255         
Hosts/Net: 254                   Class A, Private Internet

1. Requested size: 6 hosts
Netmask:   255.255.255.248 = 29 
Network:   10.10.10.0/29        
HostMin:   10.10.10.1           
HostMax:   10.10.10.6           
Broadcast: 10.10.10.7           
Hosts/Net: 6                     Class A, Private Internet

2. Requested size: 6 hosts
Netmask:   255.255.255.248 = 29 
Network:   10.10.10.8/29        
HostMin:   10.10.10.9           
HostMax:   10.10.10.14          
Broadcast: 10.10.10.15          
Hosts/Net: 6                     Class A, Private Internet

3. Requested size: 6 hosts
Netmask:   255.255.255.248 = 29 
Network:   10.10.10.16/29       
HostMin:   10.10.10.17          
HostMax:   10.10.10.22          
Broadcast: 10.10.10.23          
Hosts/Net: 6                     Class A, Private Internet

Needed size:  24 addresses.
Used network: 10.10.10.0/27
Unused:
10.10.10.24/29
10.10.10.32/27
10.10.10.64/26
10.10.10.128/25
```





