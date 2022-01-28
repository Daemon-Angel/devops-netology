# Домашнее задание к занятию "3.3. Операционные системы, лекция 1"

#### 1. Какой системный вызов делает команда `cd`? В прошлом ДЗ мы выяснили, что `cd` не является самостоятельной программой, это `shell builtin`, поэтому запустить `strace` непосредственно на `cd` не получится. Тем не менее, вы можете запустить `strace` на `/bin/bash -c 'cd /tmp'`. В этом случае вы увидите полный список системных вызовов, которые делает сам `bash` при старте. Вам нужно найти тот единственный, который относится именно к `cd`.
#### Ответ:
`user@Daemon:~$ strace /bin/bash -c 'cd /tmp'`

Системный вызов команды `cd` - `chdir("/tmp")`

#### 2. Попробуйте использовать команду `file` на объекты разных типов на файловой системе. Например:

    vagrant@netology1:~$ file /dev/tty
    /dev/tty: character special (5/0)
    vagrant@netology1:~$ file /dev/sda
    /dev/sda: block special (8/0)
    vagrant@netology1:~$ file /bin/bash
    /bin/bash: ELF 64-bit LSB shared object, x86-64

Используя `strace` выясните, где находится база данных `file` на основании которой она делает свои догадки.
#### Ответ:
Файл базы данных - `/usr/share/misc/magic.mgc`

Ещё ищет в пользовательских файлах
```
user@Daemon:~$ strace file
stat("/home/user/.magic.mgc", 0x7ffdf25aa300) = -1 ENOENT (Нет такого файла или каталога)
stat("/home/user/.magic", 0x7ffdf25aa300) = -1 ENOENT (Нет такого файла или каталога)
openat(AT_FDCWD, "/etc/magic.mgc", O_RDONLY) = -1 ENOENT (Нет такого файла или каталога)
stat("/etc/magic", {st_mode=S_IFREG|0644, st_size=111, ...}) = 0
openat(AT_FDCWD, "/etc/magic", O_RDONLY) = 3
fstat(3, {st_mode=S_IFREG|0644, st_size=111, ...}) = 0
read(3, "# Magic local data for file(1) c"..., 4096) = 111
read(3, "", 4096)                       = 0
close(3)                                = 0
openat(AT_FDCWD, "/usr/share/misc/magic.mgc", O_RDONLY) = 3
```
#### 3. Предположим, приложение пишет лог в текстовый файл. Этот файл оказался удален (deleted в lsof), однако возможности сигналом сказать приложению переоткрыть файлы или просто перезапустить приложение – нет. Так как приложение продолжает писать в удаленный файл, место на диске постепенно заканчивается. Основываясь на знаниях о перенаправлении потоков предложите способ обнуления открытого удаленного файла (чтобы освободить место на файловой системе).
#### Ответ:
Запустим процесс пинга, который пишет лог в файл test.txt, затем файл удалим, тем не менее по PID процесса видим дескриптор нашего удаленного файла, далее для обнуления с помощью `echo '' >/proc/6603/fd/1` отправим в дескриптор пустую строку.
```
root@Daemon:/home/user/Загрузки# ping 127.0.0.1 > test.txt &
[1] 6603
root@Daemon:/home/user/Загрузки# lsof -p 6603
lsof: WARNING: can't stat() fuse.gvfsd-fuse file system /run/user/1000/gvfs
      Output information may be incomplete.
lsof: WARNING: can't stat() fuse file system /run/user/1000/doc
      Output information may be incomplete.
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF   NODE NAME
ping    6603 root  cwd    DIR   8,34     4096  14825 /home/user/Загрузки
ping    6603 root  rtd    DIR   8,33     4096      2 /
ping    6603 root  txt    REG   8,33    72776  38215 /usr/bin/ping
ping    6603 root  mem    REG   8,33 14537584  44161 /usr/lib/locale/locale-archive
ping    6603 root  mem    REG   8,33   137584  45818 /usr/lib/x86_64-linux-gnu/libgpg-error.so.0.28.0
ping    6603 root  mem    REG   8,33  2029224  45434 /usr/lib/x86_64-linux-gnu/libc-2.31.so
ping    6603 root  mem    REG   8,33   101320  46401 /usr/lib/x86_64-linux-gnu/libresolv-2.31.so
ping    6603 root  mem    REG   8,33  1168056  44411 /usr/lib/x86_64-linux-gnu/libgcrypt.so.20.2.5
ping    6603 root  mem    REG   8,33    31120  45455 /usr/lib/x86_64-linux-gnu/libcap.so.2.32
ping    6603 root  mem    REG   8,33    27002  85031 /usr/lib/x86_64-linux-gnu/gconv/gconv-modules.cache
ping    6603 root  mem    REG   8,33   191472  45216 /usr/lib/x86_64-linux-gnu/ld-2.31.so
ping    6603 root  mem    REG   8,33      748 197847 /usr/share/locale-langpack/ru/LC_MESSAGES/iputils.mo
ping    6603 root    0u   CHR  136,0      0t0      3 /dev/pts/0
ping    6603 root    1w   REG   8,34    21299  36633 /home/user/Загрузки/test.txt
ping    6603 root    2u   CHR  136,0      0t0      3 /dev/pts/0
ping    6603 root    3u  icmp             0t0 106079 00000000:0003->00000000:0000
ping    6603 root    4u  sock    0,8      0t0 106080 protocol: PINGv6
root@Daemon:/home/user/Загрузки# cat test.txt
PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.027 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.037 ms
root@Daemon:/home/user/Загрузки# ^C
root@Daemon:/home/user/Загрузки# rm test.txt
root@Daemon:/home/user/Загрузки# cat test.txt
cat: test.txt: Нет такого файла или каталога
root@Daemon:/home/user/Загрузки# lsof -p 6603
lsof: WARNING: can't stat() fuse.gvfsd-fuse file system /run/user/1000/gvfs
      Output information may be incomplete.
lsof: WARNING: can't stat() fuse file system /run/user/1000/doc
      Output information may be incomplete.
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF   NODE NAME
ping    6603 root  cwd    DIR   8,34     4096  14825 /home/user/Загрузки
ping    6603 root  rtd    DIR   8,33     4096      2 /
ping    6603 root  txt    REG   8,33    72776  38215 /usr/bin/ping
ping    6603 root  mem    REG   8,33 14537584  44161 /usr/lib/locale/locale-archive
ping    6603 root  mem    REG   8,33   137584  45818 /usr/lib/x86_64-linux-gnu/libgpg-error.so.0.28.0
ping    6603 root  mem    REG   8,33  2029224  45434 /usr/lib/x86_64-linux-gnu/libc-2.31.so
ping    6603 root  mem    REG   8,33   101320  46401 /usr/lib/x86_64-linux-gnu/libresolv-2.31.so
ping    6603 root  mem    REG   8,33  1168056  44411 /usr/lib/x86_64-linux-gnu/libgcrypt.so.20.2.5
ping    6603 root  mem    REG   8,33    31120  45455 /usr/lib/x86_64-linux-gnu/libcap.so.2.32
ping    6603 root  mem    REG   8,33    27002  85031 /usr/lib/x86_64-linux-gnu/gconv/gconv-modules.cache
ping    6603 root  mem    REG   8,33   191472  45216 /usr/lib/x86_64-linux-gnu/ld-2.31.so
ping    6603 root  mem    REG   8,33      748 197847 /usr/share/locale-langpack/ru/LC_MESSAGES/iputils.mo
ping    6603 root    0u   CHR  136,0      0t0      3 /dev/pts/0
ping    6603 root    1w   REG   8,34    38055  36633 /home/user/Загрузки/test.txt (deleted)
ping    6603 root    2u   CHR  136,0      0t0      3 /dev/pts/0
ping    6603 root    3u  icmp             0t0 106079 00000000:0003->00000000:0000
ping    6603 root    4u  sock    0,8      0t0 106080 protocol: PINGv6
root@Daemon:/home/user/Загрузки# echo '' >/proc/6603/fd/1
```
#### 4. Занимают ли зомби-процессы какие-то ресурсы в ОС (CPU, RAM, IO)?
#### Ответ:
Зомби не занимают памяти (как процессы-сироты), но блокируют записи в таблице процессов, размер которой ограничен для каждого пользователя и системы в целом.
Зомби-процесс существует до тех пор, пока родительский процесс не прочитает его статус с помощью системного вызова wait(), в результате чего запись в таблице процессов будет освобождена.

#### 5. В iovisor BCC есть утилита `opensnoop`:
```
root@vagrant:~# dpkg -L bpfcc-tools | grep sbin/opensnoop
/usr/sbin/opensnoop-bpfcc
```
На какие файлы вы увидели вызовы группы `open` за первую секунду работы утилиты? Воспользуйтесь пакетом `bpfcc-tools` для Ubuntu 20.04. Дополнительные [сведения по установке](https://github.com/iovisor/bcc/blob/master/INSTALL.md).
#### Ответ:
```
user@Daemon:~$ sudo -i
root@Daemon:~# dpkg -L bpfcc-tools | grep sbin/opensnoop
/usr/sbin/opensnoop-bpfcc
root@Daemon:~# /usr/sbin/opensnoop-bpfcc
PID    COMM               FD ERR PATH
3215   MemoryPoller      116   0 /proc/meminfo
975    irqbalance          6   0 /proc/interrupts
975    irqbalance          6   0 /proc/stat
975    irqbalance          6   0 /proc/irq/28/smp_affinity
975    irqbalance          6   0 /proc/irq/24/smp_affinity
975    irqbalance          6   0 /proc/irq/29/smp_affinity
975    irqbalance          6   0 /proc/irq/31/smp_affinity
975    irqbalance          6   0 /proc/irq/18/smp_affinity
975    irqbalance          6   0 /proc/irq/17/smp_affinity
975    irqbalance          6   0 /proc/irq/26/smp_affinity
975    irqbalance          6   0 /proc/irq/5/smp_affinity
975    irqbalance          6   0 /proc/irq/0/smp_affinity
975    irqbalance          6   0 /proc/irq/6/smp_affinity
975    irqbalance          6   0 /proc/irq/8/smp_affinity
975    irqbalance          6   0 /proc/irq/9/smp_affinity
975    irqbalance          6   0 /proc/irq/14/smp_affinity
975    irqbalance          6   0 /proc/irq/15/smp_affinity
1002   thermald            9   0 /sys/class/thermal/thermal_zone0/temp
970    NetworkManager     27   0 /etc/hosts
1002   thermald            9   0 /sys/class/thermal/thermal_zone0/temp
3215   MemoryPoller       56   0 /proc/meminfo
1002   thermald            9   0 /sys/class/thermal/thermal_zone0/temp
3215   MemoryPoller       56   0 /proc/meminfo
975    irqbalance          6   0 /proc/interrupts
975    irqbalance          6   0 /proc/stat
975    irqbalance          6   0 /proc/irq/28/smp_affinity
975    irqbalance          6   0 /proc/irq/24/smp_affinity
975    irqbalance          6   0 /proc/irq/29/smp_affinity
975    irqbalance          6   0 /proc/irq/31/smp_affinity
975    irqbalance          6   0 /proc/irq/18/smp_affinity
975    irqbalance          6   0 /proc/irq/17/smp_affinity
975    irqbalance          6   0 /proc/irq/26/smp_affinity
975    irqbalance          6   0 /proc/irq/5/smp_affinity
975    irqbalance          6   0 /proc/irq/0/smp_affinity
975    irqbalance          6   0 /proc/irq/6/smp_affinity
975    irqbalance          6   0 /proc/irq/8/smp_affinity
975    irqbalance          6   0 /proc/irq/9/smp_affinity
975    irqbalance          6   0 /proc/irq/14/smp_affinity
975    irqbalance          6   0 /proc/irq/15/smp_affinity
319    systemd-journal    58   0 /proc/8034/comm
319    systemd-journal    58   0 /proc/8034/cmdline
319    systemd-journal    58   0 /proc/8034/status
319    systemd-journal    58   0 /proc/8034/attr/current
319    systemd-journal    58   0 /proc/8034/sessionid
319    systemd-journal    58   0 /proc/8034/loginuid
319    systemd-journal    58   0 /proc/8034/cgroup
319    systemd-journal    -1   2 /run/systemd/units/log-extra-fields:packagekit.service
319    systemd-journal    58   0 /var/log/journal/425981a116324619b737359ee6064c79
319    systemd-journal    -1   2 /run/log/journal/425981a116324619b737359ee6064c79/system.journal
1      systemd           131   0 /sys/fs/cgroup/unified/system.slice/packagekit.service/cgroup.procs
1      systemd           131   0 /sys/fs/cgroup/unified/system.slice/packagekit.service
1      systemd           131   0 /proc/1001/cgroup
1      systemd           131   0 /sys/fs/cgroup/unified/system.slice/packagekit.service/cgroup.events
1      systemd           131   0 /proc/8034/comm
1      systemd           131   0 /proc/8034/comm
1      systemd           131   0 /proc/8034/cgroup
1      systemd            -1   2 /sys/fs/cgroup/memory/system.slice/packagekit.service/memory.events
1      systemd           131   0 /sys/fs/cgroup/unified/system.slice/packagekit.service/cgroup.procs
1      systemd           131   0 /sys/fs/cgroup/unified/system.slice/packagekit.service
1      systemd           131   0 /sys/fs/cgroup/unified/system.slice/packagekit.service/cgroup.procs
1      systemd           131   0 /sys/fs/cgroup/unified/system.slice/packagekit.service/cgroup.threads
1      systemd           131   0 /sys/fs/cgroup/unified/system.slice/packagekit.service
1      systemd           131   0 /sys/fs/cgroup/unified/system.slice/packagekit.service
1      systemd           131   0 /sys/fs/cgroup/systemd/system.slice/packagekit.service
1      systemd           131   0 /sys/fs/cgroup/memory/system.slice/packagekit.service
```
#### 6. Какой системный вызов использует `uname -a`? Приведите цитату из man по этому системному вызову, где описывается альтернативное местоположение в `/proc`, где можно узнать версию ядра и релиз ОС.
#### Ответ:
Part of the utsname information is also accessible via /proc/sys/kernel/{ostype, hostname, osrelease, version, domainname}.

