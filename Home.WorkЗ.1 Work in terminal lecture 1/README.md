1. Установил Virtual Box 6.1

![изображение](https://user-images.githubusercontent.com/94983313/147611141-2ce7cc40-69e5-41ec-9fd0-b8b3c3d4e8d9.png)

2. Установил средство автоматизации Hashicorp Vagrant: user@Daemon:~$ sudo apt-get update && sudo apt-get install vagrant

3. В вашем основном окружении подготовьте удобный для дальнейшей работы терминал. - Выполнено.

4. Проинициализировал Vagrant: user@Daemon:~$ vagrant init hashicorp/bionic64 A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read the comments in the Vagrantfile as well as documentation on `vagrantup.com` for more information on using Vagrant.

Выполнил команду vagrant init: user@Daemon:~/.vagrant.d$ vagrant init 
A `Vagrantfile` has been placed in this directory. You are now ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on `vagrantup.com` for more information on using Vagrant.

Заменил содержимое Vagrantfile по умолчанию следующим:

 Vagrant.configure("2") do |config|
 	config.vm.box = "bento/ubuntu-20.04"
 end
 
 Выполнение в этой директории команды vagrant up
 
user@Daemon:~/.vagrant.d$ vagrant up

Bringing machine 'default' up with 'virtualbox' provider...

==> default: Box 'bento/ubuntu-20.04' could not be found. Attempting to find and install...
    default: Box Provider: virtualbox
    default: Box Version: >= 0
==> default: Loading metadata for box 'bento/ubuntu-20.04'
    default: URL: https://vagrantcloud.com/bento/ubuntu-20.04
==> default: Adding box 'bento/ubuntu-20.04' (v202112.19.0) for provider: virtualbox
    default: Downloading: https://vagrantcloud.com/bento/boxes/ubuntu-20.04/versions/202112.19.0/providers/virtualbox.box
    default: Download redirected to host: vagrantcloud-files-production.s3-accelerate.amazonaws.com
==> default: Successfully added box 'bento/ubuntu-20.04' (v202112.19.0) for 'virtualbox'!
==> default: Importing base box 'bento/ubuntu-20.04'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'bento/ubuntu-20.04' version '202112.19.0' is up to date...
==> default: Setting the name of the VM: vagrantd_default_1640819824484_77615
Vagrant is currently configured to create VirtualBox synced folders with
the `SharedFoldersEnableSymlinksCreate` option enabled. If the Vagrant
guest is not trusted, you may want to disable this option. For more
information on this option, please refer to the VirtualBox manual:

  https://www.virtualbox.org/manual/ch04.html#sharedfolders

This option can be disabled globally with an environment variable:

  VAGRANT_DISABLE_VBOXSYMLINKCREATE=1

or on a per folder basis within the Vagrantfile:

  config.vm.synced_folder '/host/path', '/guest/path', SharedFoldersEnableSymlinksCreate: false
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 (guest) => 2222 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: 
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default: 
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
==> default: Mounting shared folders...
    default: /vagrant => /home/user/.vagrant.d
    
    user@Daemon:~/.vagrant.d$ vagrant suspend
    
==> default: Saving VM state and suspending execution...

user@Daemon:~/.vagrant.d$ vagrant up

Bringing machine 'default' up with 'virtualbox' provider...
==> default: Checking if box 'bento/ubuntu-20.04' version '202112.19.0' is up to date...
==> default: Resuming suspended VM...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
==> default: Machine booted and ready!
==> default: Machine already provisioned. Run `vagrant provision` or use the `--provision`
==> default: flag to force provisioning. Provisioners marked to run always will still run.

user@Daemon:~/.vagrant.d$ vagrant halt

==> default: Attempting graceful shutdown of VM...

5. Ознакомился с графическим интерфейсом VirtualBox; Какие ресурсы выделены по-умолчанию? - Оперативная память: 1024 Mb; Процессоры: 2; Видеопаямть: 4Mb; 
HDD:64Gb

![изображение](https://user-images.githubusercontent.com/94983313/147711407-93e4f620-d684-4624-bd1f-995f6a7f3e4a.png)


6. Ознакомился с возможностями конфигурации VirtualBox через Vagrantfile: документация. 

Как добавить оперативной памяти или ресурсов процессора виртуальной машине? 

Необходимо отредактировать файл Vagrantfile добавив: 

v.memory = 1024  

v.cpus = 2

или командами ВМ

config.vm.provider "virtualbox" do |v|

vb.memory = "1024"

vb.cpu = "2"

end

7. user@Daemon:~/.vagrant.d$ vagrant ssh

Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-91-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue 04 Jan 2022 08:35:36 PM UTC

  System load:  0.26               Processes:             123
  Usage of /:   11.6% of 30.88GB   Users logged in:       0
  Memory usage: 18%                IPv4 address for eth0: 10.0.2.15
  Swap usage:   0%


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento

Попрактикуйтесь в выполнении обсуждаемых команд в терминале Ubuntu - выполнено.

8. Ознакомиться с разделами man bash, почитать о настройках самого bash:

какой переменной можно задать длину журнала history, и на какой строчке manual это описывается?

1. HISTFILESIZE - максимальное число строк в файле истории для сохранения, 
строка 1155
2. HISTSIZE - число команд для сохранения, 
строка 1178

что делает директива ignoreboth в bash?

ignoreboth это сокращение для 2х директив ignorespace and ignoredups, 
    ignorespace - не сохранять команды начинающиеся с пробела, 
    ignoredups - не сохранять команду, если такая уже имеется в истории
   
9. В каких сценариях использования применимы скобки {} и на какой строчке man bash это описано?

       { list; }
              list is simply executed in the current shell environment.   list
              must  be  terminated with a newline or semicolon.  This is known
              as a group command.  The return status is  the  exit  status  of
              list.   Note that unlike the metacharacters ( and ), { and } are
              reserved words and must occur where a reserved word is permitted
              to  be  recognized.   Since they do not cause a word break, they
              must be separated from  list  by  whitespace  or  another  shell
              metacharacter.
  Строка - 343            

10. С учётом ответа на предыдущий вопрос, как создать однократным вызовом touch 100000 файлов? Получится ли аналогичным образом создать 300000? Если нет, то почему?

user@Daemon:~/Документы$ touch {1..100000}.txt - создаст в текущей директории соответствующее число файлов

user@Daemon:~/Документы$ rm {1..100000}.txt - удалит созданные файлы

user@Daemon:~/Документы$ rm {1..300000}.txt - не удается создать такое количество файлов
bash: /usr/bin/rm: Слишком длинный список аргументов

11. В man bash поищите по /\[\[. Что делает конструкция [[ -d /tmp ]]

-d - это оператор, проверяющий, существует ли данный каталог или нет.

Пример использования в скрипте:

if [[ -d /tmp ]]
then
    echo "каталог существует"
else
    echo "каталога отсутствует"
fi

12. Основываясь на знаниях о просмотре текущих (например, PATH) и установке новых переменных; командах, которые мы рассматривали, добейтесь в выводе type -a bash в виртуальной машине наличия первым пунктом в списке:
bash is /tmp/new_path_directory/bash
bash is /usr/local/bin/bash
bash is /bin/bash
(прочие строки могут отличаться содержимым и порядком)

vagrant@vagrant:~$ mkdir /tmp/new_path_dir/

vagrant@vagrant:~$ cp /bin/bash /tmp/new_path_dir/

vagrant@vagrant:~$ type -a bash

bash is /usr/bin/bash

bash is /bin/bash

vagrant@vagrant:~$ PATH=/tmp/new_path_dir/:$PATH

vagrant@vagrant:~$ type -a bash

bash is /tmp/new_path_dir/bash

bash is /usr/bin/bash

bash is /bin/bash

13. Чем отличается планирование команд с помощью batch и at?

Команда at берет дату и время ( runtime), когда вы хотите выполнить задание в качестве параметра командной строки, и команду, которая будет выполнена из стандартного ввода.

batch или его псевдоним at -b, планирует задания и выполняет их в очереди пакетов, когда позволяет уровень загрузки системы. По умолчанию задания выполняются, когда средняя загрузка системы ниже 1,5. 

14. Завершите работу виртуальной машины чтобы не расходовать ресурсы компьютера и/или батарею ноутбука.

user@Daemon:~/.vagrant.d$ vagrant suspend 
==> default: Saving VM state and suspending execution...

