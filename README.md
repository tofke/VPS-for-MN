# VPS-for-MN
Virtual Private Server setup for hosting "Dash/PIVX like" Master Nodes.

This step by step tuto is primarly focusing on Debian/Ubuntu systems for now.

I'll update it to get you information on other Linux flavours over time (RedHat based for example).

### 1) The first step is to get yourself a decent server
An example of good VPS provider is <A href="https://www.vultr.com/?ref=7442428">VULTR</A> wich features include :<br>
<a href="https://www.vultr.com/?ref=7442428"><img src="https://www.vultr.com/media/banner_1.png" width="728" height="90"></a>
* a high number of datacenters to span your nodes accros the globe
* a high number of preinstalled images for most operating systems
* the possibility to upload your own ISO images 
* making snapshots, backups, add storage 
* the possibility to pay with BTC or BCH
* and many more ... 

### 2) When you connect to your freshly deployed server, you should update the basic OS ! 

Most preinstalled operating systems are prebuild images wich are probably not running the latest versions of software packages. On a debian based system (like Ubuntu or Mint), the upgrade process is made like this (as root) : 
```
apt-get update && apt-get upgrade -y
```
After this first update/upgrade process, you should reboot your server, as chances are you downloaded a new Linux kernel ! 
As the root user, just type "<b>reboot</b>", and you will be disconnected. Reboots on virtual machines ares very fast, as there are no hardware checks to be done (BIOS POST, RAID firmware ans so on). You can generally reconnect after only a minute or so.
```
reboot
```

## Secure access to your server :
A lot of masternode "experts" will tell you in their documentation to install and run their stuff as root ... i <ai>STRONGELY</ai> discourage you to do so ! You should take a little more precautions with your server and create a dedicated user for each task. For example, i am running +10 masternodes on a single VPS (2 Gb of RAM and 2 CPU treads). No need to "destroy the server and redo a full installation" for a simple blockchain syncronisation problem ... if they know what they do, they should not just give you such dummy advises. This sounds so unprofessional to me ... needless to say i don't listen to such advices as i run many masternodes on a single VPS ... but well you do what you want, if it is easyer for you to just run an installation script. I suggest to learn basics of Linux administration however to understand what commands you type in ...

### 3) Create a user : 
```
useradd -m -s /bin/bash username
```
Of course, in the line above you replace "<i>username</i>" with whatever you want, like "admin", "operator", "god", "me" ... 

### 4) Add this new user in the "sudo" group : 
This particular system group will let this user do administrative tasks like installing software, starting services, administer the firewall rules and so on. Running a command with the word "sudo" before it is like "becomming root" in short.
```
usermod -a -G sudo username
```
Again, in the line above you replace "<i>username</i>" with the name you just created previouly.

Once this new user is created, set a password for him with the passwd command : 
```
passwd username
```
Again, in the line above you replace "<i>username</i>" with the name you just created previouly. This command will ask you to enter the new password twice (don't worry if you see no stars or dots or whatever in the prompt as you type a password, this is normal behaviour or this command on Unix systems).

### 5) check connection with that user (connect with ssh from wherever you want)
An even more secure option would be to enable remote connections with an ssh key ... i'll explain that later when i'll have more time to update this documentation. Basically, on a MAC or Linux at home (not on a public computer, do this only on your own), create a keypair with "ssh-keygen" and then install it to the remote user's home folder with "ssh-copy-id user@remotehost" ... same syntax as an ssh connection : "ssh user@host". I recommend using MobaXterm for Windows users, as this software let's you keep your settings if you enable a local persistent home (this will be documented here too one day).

### 6) Optionally (recommended), disable remote root login :
Do this only if the connexion with your new user works ! 
```
vi /etc/ssh/sshd_config
```
--> change "PermitRootLogin" to "no"
(you can use nano instead of vi if you prefer)

## install dependencies for bitcoin : 
(All coins out there are forks of the original Bitcoin Core software stack)
1) Add some components used by most of the masternode software (unless you compile yourself, then you probably know what you do and won't need this documentation). Always refer to the dev team install notice when possible for needed dependencies.
```
sudo apt install software-properties-common
sudo add-apt-repository ppa:bitcoin/bitcoin
sudo apt update && sudo apt upgrade
sudo apt-get install build-essential libtool autotools-dev autoconf pkg-config libssl-dev libevent-dev
sudo apt install libssl1.0.0 libboost-all-dev libdb4.8++ libdb4.8++-dev libzmq5 libminiupnpc10
```
#### NOTE : for old Ubuntu 14.04, replace the last line with : 
```
sudo apt install libssl1.0.0 libboost-all-dev libdb4.8++ libdb4.8++-dev libzmq3 libminiupnpc8
```
### optionnal (but recommended) :
To enable building of the QT graphacal user interfaces.
```
sudo apt install qt protobuf libqrencode 
```

2) Add some swap space (virtual memory on disk like 'pagefile.sys' on Windows) : 
```
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```
Depending on your needs, you could add more or less swap : 4G stands for 4 GigaBytes, so if for example you just want a 2 Gb swapfile you would have typed "fallocate -l 2G /swapfile" instead.
### NOTE : type this last command as root, not with "sudo" !
Become root : 
```
sudo su -
```
Add the swapfile in your fstab to have it enabled automatically after a reboot : 
```
echo "/swapfile   none    swap    sw    0   0" >> /etc/fstab
```

3) Tell Linux kernel not to swap unless necessary, for example in case of RAM shortage under 10% :  
Check current settings with "sysctl -a|grep swappiness", and if needed change it (as root) :
```
sysctl -a|grep swappiness
```
For example, you could have this kind of output : 
(this is an example, don't paste these lines)
```
root@mine:~# sysctl -a|grep swappiness
sysctl: lecture de la clé « net.ipv6.conf.all.stable_secret »
sysctl: lecture de la clé « net.ipv6.conf.default.stable_secret »
sysctl: lecture de la clé « net.ipv6.conf.enp4s0.stable_secret »
sysctl: lecture de la clé « net.ipv6.conf.lo.stable_secret »
vm.swappiness = 60
```
(ignore the other messages beginning with "sysctl:" here)
--> let's change that in "/etc/sysctl.conf" to "vm.swappiness=10", and run "sysctl -p" to reload : 
```
echo "vm.swappiness=10" >> /etc/sysctl.conf && sysctl -p
```
This last command should answer with the changed parameter, for example : 
(again, this is just an example, don't paste "root@mine:#" in your shell)
```
root@mine:~# echo "vm.swappiness=10" >> /etc/sysctl.conf && sysctl -p
vm.swappiness = 10
```

3) Add some more tools to ease administration : 
```
sudo apt install htop glances byobu jq -y
```
(did you notice i use sudo again : connect as root only when needed)
* htop : a better top with easy viewable ressource usage
* glances : an even better tool to view ressources "at a glance" (hence the name, comes from HP-UX 'glance' tool)
* byobu : a better 'screen' (a terminal multiplexer, wich allow to keep a detached session in backgroud or share your screen with multiple sessions)
* jq : jq is like sed for JSON data (a usefull tool to parse JSON data)

### Tools for Windows (for remote administration of your VPS)
* MobaXterm : the best Xwindows / SSH client (and much more) i know for Windows : https://mobaxterm.mobatek.net/download.html

