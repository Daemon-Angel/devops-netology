# Домашнее задание к занятию "3.8. Компьютерные сети, лекция 3"
#### 1. Подключитесь к публичному маршрутизатору в интернет. Найдите маршрут к вашему публичному IP
```
telnet route-views.routeviews.org
Username: rviews
show ip route x.x.x.x/32
show bgp x.x.x.x/32
```
#### Ответ: 
```
route-views>show ip route 89.109.47.215   
Routing entry for 89.109.47.0/24
  Known via "bgp 6447", distance 20, metric 0
  Tag 49788, type external
  Last update from 91.218.184.60 2d16h ago
  Routing Descriptor Blocks:
  * 91.218.184.60, from 91.218.184.60, 2d16h ago
      Route metric is 0, traffic share count is 1
      AS Hops 3
      Route tag 49788
      MPLS label: none
```
#### 2. Создайте dummy0 интерфейс в Ubuntu. Добавьте несколько статических маршрутов. Проверьте таблицу маршрутизации.
#### Ответ:
Запуск модуля
```
# echo "dummy" > /etc/modules-load.d/dummy.conf
# echo "options dummy numdummies=2" > /etc/modprobe.d/dummy.conf
```
Настройка интерфейса через `systemd`

       cat << "EOF" >> /etc/systemd/network/10-dummy0.netdev
       [NetDev]
       Name=dummy0
       Kind=dummy
       EOF
       cat << "EOF" >> /etc/systemd/network/20-dummy0.network
       [Match]
       Name=dummy0
       
       [Network]
       Address=10.0.8.1/24
       EOF

 Рестарт

       # systemctl restart systemd-networkd

Добавлен маршрут, конфигурация через `netplan`

           ens4:
             optional: true
             addresses:
               - 10.0.0.3/24
             routes:
               - to: 10.0.4.0/24
                 via: 10.0.0.2

Таблица маршрутизации, статический маршрут - с меткой `static`:

       root@Node2:~# ip r
       default via 192.168.255.1 dev bond0 proto dhcp src 192.168.255.15 metric 100
       10.0.0.0/24 dev ens4 proto kernel scope link src 10.0.0.3
       10.0.1.0/24 dev vlan1001 proto kernel scope link src 10.0.1.3
       10.0.4.0/24 via 10.0.0.2 dev ens4 proto static
       10.0.8.0/24 dev dummy0 proto kernel scope link src 10.0.8.1
       192.168.255.0/24 dev bond0 proto kernel scope link src 192.168.255.15
       192.168.255.1 dev bond0 proto dhcp scope link src 192.168.255.15 metric 100

       root@Node2:~# ip r | grep stat
       10.0.4.0/24 via 10.0.0.2 dev ens4 proto static
#### 3. Проверьте открытые TCP порты в Ubuntu, какие протоколы и приложения используют эти порты? Приведите несколько примеров.
#### Ответ:
```
root@Daemon:/home/user# ss -tnlp
State      Recv-Q     Send-Q           Local Address:Port            Peer Address:Port     Process                                                                                    
LISTEN     0          4096                   0.0.0.0:111                  0.0.0.0:*         users:(("rpcbind",pid=791,fd=4),("systemd",pid=1,fd=69))                                  
LISTEN     0          511                    0.0.0.0:80                   0.0.0.0:*         users:(("nginx",pid=1146,fd=6),("nginx",pid=1145,fd=6),("nginx",pid=1144,fd=6),("nginx",pid=1143,fd=6),("nginx",pid=1140,fd=6))
LISTEN     0          4096             127.0.0.53%lo:53                   0.0.0.0:*         users:(("systemd-resolve",pid=792,fd=13))
```
53 порт - это DNS.

80 порт - nginx.

#### 4. Проверьте используемые UDP сокеты в Ubuntu, какие протоколы и приложения используют эти порты?
#### Ответ:
```
root@Daemon:/home/user# ss -unap
State          Recv-Q         Send-Q                          Local Address:Port                    Peer Address:Port         Process 
UNCONN         0              0                               127.0.0.53%lo:53                           0.0.0.0:*             users:(("systemd-resolve",pid=792,fd=12))                        
ESTAB          0              0                        192.168.0.110%enp3s0:68                       192.168.0.1:67            users:(("NetworkManager",pid=951,fd=23)) 
```
53 порт - так же DNS.

68 порт использует DHCP для отправки сообщений клиентам.
#### 5. Используя diagrams.net, создайте L3 диаграмму вашей домашней сети или любой другой сети, с которой вы работали.
#### Ответ:


