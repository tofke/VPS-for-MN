# VPS-for-MN
Virtual Private Server setup for hosting Master Nodes

== secure access :
1 create a user
2 add in sudo group
3 check connection with that user
4 disable root login

== install dependencies : 
 sudo add-apt-repository ppa:bitcoin/bitcoin
 sudo apt update && sudo apt upgrade
 sudo install libssl libboost 
=== optionnal : 
 sudo install miniupnpc (firewall-jumping support)
 sudo install libdb4.8 (needed if wallet enabled)
 sudo install qt protobuf libqrencode (needed if gui enabled)
 
