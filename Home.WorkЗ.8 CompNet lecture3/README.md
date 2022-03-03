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
```
# echo "dummy" > /etc/modules-load.d/dummy.conf
# echo "options dummy numdummies=2" > /etc/modprobe.d/dummy.conf
```
#### 3. Проверьте открытые TCP порты в Ubuntu, какие протоколы и приложения используют эти порты? Приведите несколько примеров.
#### Ответ:

