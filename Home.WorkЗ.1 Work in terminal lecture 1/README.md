Установил Virtual Box 6.1

![изображение](https://user-images.githubusercontent.com/94983313/147611141-2ce7cc40-69e5-41ec-9fd0-b8b3c3d4e8d9.png)

Установил средство автоматизации Hashicorp Vagrant: user@Daemon:~$ sudo apt-get update && sudo apt-get install vagrant

Проинициализировал Vagrant: user@Daemon:~$ vagrant init hashicorp/bionic64 A `Vagrantfile` has been placed in this directory. You are now
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

Ознакомился с графическим интерфейсом VirtualBox; Какие ресурсы выделены по-умолчанию? - Система, Дисплей, Аудио, Сеть, COM-порты, USB

![изображение](https://user-images.githubusercontent.com/94983313/147711407-93e4f620-d684-4624-bd1f-995f6a7f3e4a.png)


Ознакомился с возможностями конфигурации VirtualBox через Vagrantfile: документация. 

Как добавить оперативной памяти или ресурсов процессора виртуальной машине? 

config.vm.provider "virtualbox" do |v|

v.memory = 1024 

v.cpus = 2

end



