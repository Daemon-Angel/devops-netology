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




