# VPS-for-MN
Virtual Private Server setup for hosting "Dash like" Master Nodes

## secure access :
1) create a user : 
```
useradd -m -s /bin/bash username
```
2) add in sudo group : 
```
usermod -g username -G sudo username
```
3) check connection with that user (connect with ssh from wherever you want)
4) disable root login (vi /etc/ssh/sshd_config : PermitRootLogin no)

## install dependencies : 
```
sudo apt install software-properties-common
sudo add-apt-repository ppa:bitcoin/bitcoin
sudo apt update && sudo apt upgrade
sudo apt install libssl1.0.0 libboost-system1.58.0 libboost-program-options1.58.0 libboost-thread1.58.0 libboost-chrono1.58.0 libdb4.8++
```
=> Search where you compiled with :
```
for file in $(ldd yourcompiledbinary|cut -d ' ' -f 3); do dpkg -S $file|cut -d ':' -f1;done
```
Then install resulting package names : 
```
sudo apt install libzmq5 libboost-filesystem1.58.0 libboost-program-options1.58.0 libdb4.8++ libminiupnpc10
```

### optionnal (but recommended) :
1) Add some components used by most of the masternode software (unless you compile yourself)
```
sudo apt install libminiupnpc10 (firewall-jumping support)
sudo apt install libdb4.8 (needed if wallet enabled)
sudo apt install qt protobuf libqrencode (needed if gui enabled)
```
2) Add some swap space (virtual memory on disk like 'pagefile.sys' on Windows) : 
```
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
sudo echo "/swapfile   none    swap    sw    0   0" >> /etc/fstab
```
Depending on your needs, you could add more or less swap : 4G stands for 4 GigaBytes, so if for example you just want some a 2 Gb swapfile you would have typed "fallocate -l 2G /swapfile" instead.
3) Tell Linux kernel not to swap unless necessary, for example in case of RAM shortage under 10% :  
```
sudo echo "vm.swappiness=10" >> /etc/sysctl.conf
sudo sysctl -p
```
3) Add some more tools to ease administration : 
```
sudo apt install htop glances byobu -y
```
* htop : a better top with easy viewable ressource usage
* glances : an even better tool to view ressources "at a glance" (hence the name, comes from HP-UX 'glance' tool)
* byobu : a better 'screen' (a terminal multiplexer, wich allow to keep a detached session in backgroud or share your screen with multiple sessions)

### Tools for Windows (for remote administration of your VPS)
* MobaXterm : the best Xwindows / SSH client (and much more) i know for Windows : https://mobaxterm.mobatek.net/download.html

