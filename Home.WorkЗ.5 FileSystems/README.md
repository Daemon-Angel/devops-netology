# Домашнее задание к занятию "3.5. Файловые системы"

#### 1. Узнайте о [sparse](https://ru.wikipedia.org/wiki/%D0%A0%D0%B0%D0%B7%D1%80%D0%B5%D0%B6%D1%91%D0%BD%D0%BD%D1%8B%D0%B9_%D1%84%D0%B0%D0%B9%D0%BB) (разряженных) файлах.
#### Ответ: 
Прочитал данную информацию, интересное решение, ранее почему-то не слышал о таком типе файлов.
#### 2. Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?
#### Ответ: Жесткая ссылка и файл, для которой она создавалась имеют одинаковые inode. Поэтому жесткая ссылка имеет те же права доступа, владельца и время последней модификации, что и целевой файл. Различаются только имена файлов. Фактически жесткая ссылка это еще одно имя для файла.
Пример:
```
user@Daemon:~$ touch test_hardlink
user@Daemon:~$ ln test_hardlink test_hardlink_1
user@Daemon:~$ ls -ilh
  28964 -rw-rw-r--  2 user user    0 фев 10 01:54  test_hardlink
  28964 -rw-rw-r--  2 user user    0 фев 10 01:54  test_hardlink_1
user@Daemon:~$ chmod 777 test_hardlink
user@Daemon:~$ ls -ilh
  28964 -rwxrwxrwx  2 user user    0 фев 10 01:54  test_hardlink
  28964 -rwxrwxrwx  2 user user    0 фев 10 01:54  test_hardlink_1
```
#### 3. Сделайте `vagrant destroy` на имеющийся инстанс Ubuntu. Замените содержимое Vagrantfile следующим:
    Vagrant.configure("2") do |config|
      config.vm.box = "bento/ubuntu-20.04"
      config.vm.provider :virtualbox do |vb|
        lvm_experiments_disk0_path = "/tmp/lvm_experiments_disk0.vmdk"
        lvm_experiments_disk1_path = "/tmp/lvm_experiments_disk1.vmdk"
        vb.customize ['createmedium', '--filename', lvm_experiments_disk0_path, '--size', 2560]
        vb.customize ['createmedium', '--filename', lvm_experiments_disk1_path, '--size', 2560]
        vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 1, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk0_path]
        vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 2, '--device', 0, '--type', 'hdd', '--medium', lvm_experiments_disk1_path]
      end
    end
Данная конфигурация создаст новую виртуальную машину с двумя дополнительными неразмеченными дисками по 2.5 Гб.
#### Ответ: 
Выполнено:
```
user@Daemon:~/.vagrant.d$ vagrant destroy
    default: Are you sure you want to destroy the 'default' VM? [y/N] y
==> default: Destroying VM and associated drives...
vagrant@vagrant:~$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0                       7:0    0 55.4M  1 loop /snap/core18/2128
loop1                       7:1    0 32.3M  1 loop /snap/snapd/12704
loop2                       7:2    0 70.3M  1 loop /snap/lxd/21029
loop3                       7:3    0 55.5M  1 loop /snap/core18/2284
loop4                       7:4    0 43.4M  1 loop /snap/snapd/14549
loop5                       7:5    0 61.9M  1 loop /snap/core20/1328
sda                         8:0    0   64G  0 disk 
├─sda1                      8:1    0    1M  0 part 
├─sda2                      8:2    0    1G  0 part /boot
└─sda3                      8:3    0   63G  0 part 
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm  /
sdb                         8:16   0  2.5G  0 disk 
sdc                         8:32   0  2.5G  0 disk 
```
#### 4. Используя `fdisk`, разбейте первый диск на 2 раздела: 2 Гб, оставшееся пространство.
#### Ответ:
```
vagrant@vagrant:~$ sudo fdisk /dev/sdb

Welcome to fdisk (util-linux 2.34).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0x2cc4936f.
Command (m for help): F
Unpartitioned space /dev/sdb: 2.51 GiB, 2683305984 bytes, 5240832 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes

Start     End Sectors  Size
 2048 5242879 5240832  2.5G

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 
First sector (2048-5242879, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-5242879, default 5242879): +2G

Created a new partition 1 of type 'Linux' and of size 2 GiB.

Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (2-4, default 2): 
First sector (4196352-5242879, default 4196352): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (4196352-5242879, default 5242879): 

Created a new partition 2 of type 'Linux' and of size 511 MiB.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
vagrant@vagrant:~$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0                       7:0    0 55.4M  1 loop /snap/core18/2128
loop1                       7:1    0 32.3M  1 loop /snap/snapd/12704
loop2                       7:2    0 70.3M  1 loop /snap/lxd/21029
loop3                       7:3    0 55.5M  1 loop /snap/core18/2284
loop4                       7:4    0 43.4M  1 loop /snap/snapd/14549
loop5                       7:5    0 61.9M  1 loop /snap/core20/1328
loop6                       7:6    0 67.2M  1 loop /snap/lxd/21835
sda                         8:0    0   64G  0 disk 
├─sda1                      8:1    0    1M  0 part 
├─sda2                      8:2    0    1G  0 part /boot
└─sda3                      8:3    0   63G  0 part 
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm  /
sdb                         8:16   0  2.5G  0 disk 
├─sdb1                      8:17   0    2G  0 part 
└─sdb2                      8:18   0  511M  0 part 
sdc                         8:32   0  2.5G  0 disk 
```
#### 5. Используя `sfdisk`, перенесите данную таблицу разделов на второй диск.
#### Ответ:
```
vagrant@vagrant:~$ sudo sfdisk -d /dev/sdb > sdb.dump
vagrant@vagrant:~$ sudo sfdisk /dev/sdc < sdb.dump
Checking that no-one is using this disk right now ... OK

Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

>>> Script header accepted.
>>> Script header accepted.
>>> Script header accepted.
>>> Script header accepted.
>>> Created a new DOS disklabel with disk identifier 0x2cc4936f.
/dev/sdc1: Created a new partition 1 of type 'Linux' and of size 2 GiB.
/dev/sdc2: Created a new partition 2 of type 'Linux' and of size 511 MiB.
/dev/sdc3: Done.

New situation:
Disklabel type: dos
Disk identifier: 0x2cc4936f

Device     Boot   Start     End Sectors  Size Id Type
/dev/sdc1          2048 4196351 4194304    2G 83 Linux
/dev/sdc2       4196352 5242879 1046528  511M 83 Linux

The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
vagrant@vagrant:~$ lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0                       7:0    0 55.4M  1 loop /snap/core18/2128
loop1                       7:1    0 32.3M  1 loop /snap/snapd/12704
loop2                       7:2    0 70.3M  1 loop /snap/lxd/21029
loop3                       7:3    0 55.5M  1 loop /snap/core18/2284
loop4                       7:4    0 43.4M  1 loop /snap/snapd/14549
loop5                       7:5    0 61.9M  1 loop /snap/core20/1328
loop6                       7:6    0 67.2M  1 loop /snap/lxd/21835
sda                         8:0    0   64G  0 disk 
├─sda1                      8:1    0    1M  0 part 
├─sda2                      8:2    0    1G  0 part /boot
└─sda3                      8:3    0   63G  0 part 
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm  /
sdb                         8:16   0  2.5G  0 disk 
├─sdb1                      8:17   0    2G  0 part 
└─sdb2                      8:18   0  511M  0 part 
sdc                         8:32   0  2.5G  0 disk 
├─sdc1                      8:33   0    2G  0 part 
└─sdc2                      8:34   0  511M  0 part 
```
#### 6. Соберите `mdadm` RAID1 на паре разделов 2 Гб.
#### Ответ:
```
vagrant@vagrant:~$ sudo -i
root@vagrant:~# mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sd[bc]1
mdadm: Note: this array has metadata at the start and
    may not be suitable as a boot device.  If you plan to
    store '/boot' on this device please ensure that
    your boot-loader understands md/v1.x metadata, or use
    --metadata=0.90
Continue creating array? y
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md0 started.
root@vagrant:~# lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                       7:0    0 55.4M  1 loop  /snap/core18/2128
loop1                       7:1    0 32.3M  1 loop  /snap/snapd/12704
loop2                       7:2    0 70.3M  1 loop  /snap/lxd/21029
loop3                       7:3    0 55.5M  1 loop  /snap/core18/2284
loop4                       7:4    0 43.4M  1 loop  /snap/snapd/14549
loop5                       7:5    0 61.9M  1 loop  /snap/core20/1328
loop6                       7:6    0 67.2M  1 loop  /snap/lxd/21835
sda                         8:0    0   64G  0 disk  
├─sda1                      8:1    0    1M  0 part  
├─sda2                      8:2    0    1G  0 part  /boot
└─sda3                      8:3    0   63G  0 part  
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm   /
sdb                         8:16   0  2.5G  0 disk  
├─sdb1                      8:17   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
└─sdb2                      8:18   0  511M  0 part  
sdc                         8:32   0  2.5G  0 disk  
├─sdc1                      8:33   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
└─sdc2                      8:34   0  511M  0 part
```
#### 7. Соберите `mdadm` RAID0 на второй паре маленьких разделов.
#### Ответ:
```
root@vagrant:~# mdadm --create /dev/md1 --level=0 --raid-devices=2 /dev/sd[bc]2
mdadm: Defaulting to version 1.2 metadata
mdadm: array /dev/md1 started.
root@vagrant:~# lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                       7:0    0 55.4M  1 loop  /snap/core18/2128
loop1                       7:1    0 32.3M  1 loop  /snap/snapd/12704
loop2                       7:2    0 70.3M  1 loop  /snap/lxd/21029
loop3                       7:3    0 55.5M  1 loop  /snap/core18/2284
loop4                       7:4    0 43.4M  1 loop  /snap/snapd/14549
loop5                       7:5    0 61.9M  1 loop  /snap/core20/1328
loop6                       7:6    0 67.2M  1 loop  /snap/lxd/21835
sda                         8:0    0   64G  0 disk  
├─sda1                      8:1    0    1M  0 part  
├─sda2                      8:2    0    1G  0 part  /boot
└─sda3                      8:3    0   63G  0 part  
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm   /
sdb                         8:16   0  2.5G  0 disk  
├─sdb1                      8:17   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
└─sdb2                      8:18   0  511M  0 part  
  └─md1                     9:1    0 1018M  0 raid0 
sdc                         8:32   0  2.5G  0 disk  
├─sdc1                      8:33   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
└─sdc2                      8:34   0  511M  0 part  
  └─md1                     9:1    0 1018M  0 raid0
```
#### 8. Создайте 2 независимых PV на получившихся md-устройствах.
#### Ответ:
```
root@vagrant:~# pvcreate /dev/md0
  Physical volume "/dev/md0" successfully created.
root@vagrant:~# pvcreate /dev/md1
  Physical volume "/dev/md1" successfully created.
root@vagrant:~# pvs
  PV         VG        Fmt  Attr PSize    PFree   
  /dev/md0             lvm2 ---    <2.00g   <2.00g
  /dev/md1             lvm2 ---  1018.00m 1018.00m
  /dev/sda3  ubuntu-vg lvm2 a--   <63.00g  <31.50g
  ```
#### 9. Создайте общую volume-group на этих двух PV.
#### Ответ:
```
root@vagrant:~# vgcreate myvg /dev/md0 /dev/md1
  Volume group "myvg" successfully created
root@vagrant:~# vgs
  VG        #PV #LV #SN Attr   VSize   VFree  
  myvg        2   0   0 wz--n-  <2.99g  <2.99g
  ubuntu-vg   1   1   0 wz--n- <63.00g <31.50g
```
#### 10. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.
#### Ответ:
```
root@vagrant:~# lvcreate -L 100Mb -n mylv /dev/myvg /dev/md1
  Logical volume "mylv" created.
root@vagrant:~# lvs
  LV        VG        Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  mylv      myvg      -wi-a----- 100.00m                                                    
  ubuntu-lv ubuntu-vg -wi-ao----  31.50g
```
#### 11. Создайте `mkfs.ext4` ФС на получившемся LV.
#### Ответ:
```
root@vagrant:~# mkfs.ext4 /dev/myvg/mylv
mke2fs 1.45.5 (07-Jan-2020)
Creating filesystem with 25600 4k blocks and 25600 inodes

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (1024 blocks): done
Writing superblocks and filesystem accounting information: done
```
#### 12. Смонтируйте этот раздел в любую директорию, например, `/tmp/new`.
#### Ответ:
```
root@vagrant:~# mkdir /tmp/new
root@vagrant:~# mount /dev/myvg/mylv /tmp/new
root@vagrant:~# df -h
Filesystem                         Size  Used Avail Use% Mounted on
udev                               447M     0  447M   0% /dev
tmpfs                               99M 1012K   98M   2% /run
/dev/mapper/ubuntu--vg-ubuntu--lv   31G  3.7G   26G  13% /
tmpfs                              491M     0  491M   0% /dev/shm
tmpfs                              5.0M     0  5.0M   0% /run/lock
tmpfs                              491M     0  491M   0% /sys/fs/cgroup
/dev/loop0                          56M   56M     0 100% /snap/core18/2128
/dev/loop1                          33M   33M     0 100% /snap/snapd/12704
/dev/loop2                          71M   71M     0 100% /snap/lxd/21029
/dev/sda2                          976M  107M  803M  12% /boot
vagrant                             42G   24G   18G  58% /vagrant
/dev/loop3                          56M   56M     0 100% /snap/core18/2284
/dev/loop4                          44M   44M     0 100% /snap/snapd/14549
tmpfs                               99M     0   99M   0% /run/user/1000
/dev/loop5                          62M   62M     0 100% /snap/core20/1328
/dev/loop6                          68M   68M     0 100% /snap/lxd/21835
/dev/mapper/myvg-mylv               93M   72K   86M   1% /tmp/new
```
#### 13. Поместите туда тестовый файл, например `wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz`.
#### Ответ:
```
root@vagrant:~# cd /tmp/new/
root@vagrant:/tmp/new# wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz
--2022-02-13 23:01:43--  https://mirror.yandex.ru/ubuntu/ls-lR.gz
Resolving mirror.yandex.ru (mirror.yandex.ru)... 213.180.204.183
Connecting to mirror.yandex.ru (mirror.yandex.ru)|213.180.204.183|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 22269769 (21M) [application/octet-stream]
Saving to: ‘/tmp/new/test.gz’

/tmp/new/test.gz        100%[============================>]  21.24M  6.67MB/s    in 3.2s    

2022-02-13 23:01:46 (6.56 MB/s) - ‘/tmp/new/test.gz’ saved [22269769/22269769
root@vagrant:/tmp/new# ls -l
total 21764
drwx------ 2 root root    16384 Feb 13 22:47 lost+found
-rw-r--r-- 1 root root 22269769 Feb 13 18:18 test.gz
```
#### 14. Прикрепите вывод `lsblk`.
#### Ответ:
```
root@vagrant:/tmp/new# lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                       7:0    0 55.4M  1 loop  /snap/core18/2128
loop1                       7:1    0 32.3M  1 loop  /snap/snapd/12704
loop2                       7:2    0 70.3M  1 loop  /snap/lxd/21029
loop3                       7:3    0 55.5M  1 loop  /snap/core18/2284
loop4                       7:4    0 43.4M  1 loop  /snap/snapd/14549
loop5                       7:5    0 61.9M  1 loop  /snap/core20/1328
loop6                       7:6    0 67.2M  1 loop  /snap/lxd/21835
sda                         8:0    0   64G  0 disk  
├─sda1                      8:1    0    1M  0 part  
├─sda2                      8:2    0    1G  0 part  /boot
└─sda3                      8:3    0   63G  0 part  
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm   /
sdb                         8:16   0  2.5G  0 disk  
├─sdb1                      8:17   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
└─sdb2                      8:18   0  511M  0 part  
  └─md1                     9:1    0 1018M  0 raid0 
    └─myvg-mylv           253:1    0  100M  0 lvm   /tmp/new
sdc                         8:32   0  2.5G  0 disk  
├─sdc1                      8:33   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
└─sdc2                      8:34   0  511M  0 part  
  └─md1                     9:1    0 1018M  0 raid0 
    └─myvg-mylv           253:1    0  100M  0 lvm   /tmp/new
```
#### 15. Протестируйте целостность файла:
```
root@vagrant:~# gzip -t /tmp/new/test.gz
root@vagrant:~# echo $?
0
```
#### Ответ:
```
root@vagrant:/tmp/new# gzip -t /tmp/new/test.gz
root@vagrant:/tmp/new# echo $?
0
```
#### 16. Используя pvmove, переместите содержимое PV с RAID0 на RAID1.
#### Ответ:
```
root@vagrant:/tmp/new# pvmove /dev/md1 /dev/md0
  /dev/md1: Moved: 20.00%
  /dev/md1: Moved: 100.00%
root@vagrant:/tmp/new# lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
loop0                       7:0    0 55.4M  1 loop  /snap/core18/2128
loop1                       7:1    0 32.3M  1 loop  /snap/snapd/12704
loop2                       7:2    0 70.3M  1 loop  /snap/lxd/21029
loop3                       7:3    0 55.5M  1 loop  /snap/core18/2284
loop4                       7:4    0 43.4M  1 loop  /snap/snapd/14549
loop5                       7:5    0 61.9M  1 loop  /snap/core20/1328
loop6                       7:6    0 67.2M  1 loop  /snap/lxd/21835
sda                         8:0    0   64G  0 disk  
├─sda1                      8:1    0    1M  0 part  
├─sda2                      8:2    0    1G  0 part  /boot
└─sda3                      8:3    0   63G  0 part  
  └─ubuntu--vg-ubuntu--lv 253:0    0 31.5G  0 lvm   /
sdb                         8:16   0  2.5G  0 disk  
├─sdb1                      8:17   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
│   └─myvg-mylv           253:1    0  100M  0 lvm   /tmp/new
└─sdb2                      8:18   0  511M  0 part  
  └─md1                     9:1    0 1018M  0 raid0 
sdc                         8:32   0  2.5G  0 disk  
├─sdc1                      8:33   0    2G  0 part  
│ └─md0                     9:0    0    2G  0 raid1 
│   └─myvg-mylv           253:1    0  100M  0 lvm   /tmp/new
└─sdc2                      8:34   0  511M  0 part  
  └─md1                     9:1    0 1018M  0 raid0 
```
#### 17. Сделайте `--fail` на устройство в вашем RAID1 md.
#### Ответ:
```
root@vagrant:/tmp/new# mdadm --fail /dev/md0 /dev/sdb1
mdadm: set /dev/sdb1 faulty in /dev/md0
```
#### 18. Подтвердите выводом `dmesg`, что RAID1 работает в деградированном состоянии.
#### Ответ:
```
root@vagrant:/tmp/new# dmesg |grep raid1
[ 3463.173949] md/raid1:md0: not clean -- starting background reconstruction
[ 3463.173951] md/raid1:md0: active with 2 out of 2 mirrors
[ 7465.882446] md/raid1:md0: Disk failure on sdb1, disabling device.
               md/raid1:md0: Operation continuing on 1 devices.
```
#### 19. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:
```
root@vagrant:~# gzip -t /tmp/new/test.gz
root@vagrant:~# echo $?
0
```
#### Ответ:
```
root@vagrant:/tmp/new# gzip -t /tmp/new/test.gz
root@vagrant:/tmp/new# echo $?
0
```
#### 20. Погасите тестовый хост, `vagrant destroy`.
#### Ответ:
```
user@Daemon:~/.vagrant.d$ vagrant destroy 
    default: Are you sure you want to destroy the 'default' VM? [y/N] y
==> default: Forcing shutdown of VM...
==> default: Destroying VM and associated drives...
```








