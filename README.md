# VPS-for-MN
Virtual Private Server setup for hosting "dash like" Master Nodes

## secure access :
1) create a user : useradd -m -s /bin/bash username
2) add in sudo group : usermod -g initialgroup -G sudo username
3) check connection with that user (connect with ssh from werever you want)
4) disable root login (vi /etc/ssh/sshd_config : PermitRootLogin no)

## install dependencies : 
1) sudo apt install software-properties-common
2) sudo add-apt-repository ppa:bitcoin/bitcoin
3) sudo apt update && sudo apt upgrade
4) sudo apt install libssl1.0.0 libboost-system1.58.0 libboost-program-options1.58.0 libboost-thread1.58.0 libboost-chrono1.58.0 libdb4.8++
<p>=> search where you compiled with :
  <b>"for file in $(ldd yourcompiledbinary|cut -d ' ' -f 3); do dpkg -S $file|cut -d ':' -f1;done"</b>
  sudo apt install libzmq5 libboost-filesystem1.58.0 libboost-program-options1.58.0 libdb4.8++ libminiupnpc10
### optionnal : 
5) sudo apt install libminiupnpc10 (firewall-jumping support)
6) sudo apt install libdb4.8 (needed if wallet enabled)
7) sudo apt install qt protobuf libqrencode (needed if gui enabled)
