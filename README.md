# B1 Réseau 2019 - TP6

## [](https://github.com/Genisys33/CCNA/blob/master/TP6.md#sommaire)Sommaire

-   [Lab 1 : Simple OSPF](https://github.com/Genisys33/CCNA/blob/master/TP6.md#lab-1--simple-ospf)
-   [Lab 2 : Un peu de complexité (et d'utilité ?)](https://github.com/Genisys33/CCNA/blob/master/TP6.md#lab-2--un-peu-de-complexit%C3%A9-et-dutilit%C3%A9-)
    -   [1. Présentation du lab et contexte](https://github.com/Genisys33/CCNA/blob/master/TP6.md#1-pr%C3%A9sentation-du-lab-et-contexte)
        -   [Schéma de la topologie](https://github.com/Genisys33/CCNA/blob/master/TP6.md#sch%C3%A9ma-de-la-topologie)
        -   [Aires OSPF](https://github.com/Genisys33/CCNA/blob/master/TP6.md#aires-ospf)
        -   [Réseaux IP et aires OSPF](https://github.com/Genisys33/CCNA/blob/master/TP6.md#r%C3%A9seaux-ip-et-aires-ospf)
        -   [Adressage IP des machines](https://github.com/Genisys33/CCNA/blob/master/TP6.md#adressage-ip-de-chacune-des-machines)
    -   [2. Mise en place du lab](https://github.com/Genisys33/CCNA/blob/master/TP6.md#2-mise-en-place-du-lab)
        -   [Adressage statique - routeurs](https://github.com/Genisys33/CCNA/blob/master/TP6.md#checklist-ip-routeurs)
        -   [Adressage statique - VMs](https://github.com/Genisys33/CCNA/blob/master/TP6.md#checklist-vms)
        -   [OSPF](https://github.com/Genisys33/CCNA/blob/master/TP6.md#configuration-de-ospf)
-   [Lab 3 : Let's end this properly](https://github.com/Genisys33/CCNA/blob/master/TP6.md#lab-3--lets-end-this-properly)
    -   [NAT (accès internet dans tout le réseau)](https://github.com/Genisys33/CCNA/blob/master/TP6.md#1-nat--acc%C3%A8s-internet)
    -   [Un service d'infra](https://github.com/Genisys33/CCNA/blob/master/TP6.md#2-un-service-dinfra)
    -   [DHCP (adressage dynamique pour les clients)](https://github.com/Genisys33/CCNA/blob/master/TP6.md#3-serveur-dhcp)
    -   [DNS](https://github.com/Genisys33/CCNA/blob/master/TP6.md#4-serveur-dns)
    -   [NTP (synchronisation de l'heure)](https://github.com/Genisys33/CCNA/blob/master/TP6.md#5-serveur-ntp)
-   [Bilan](https://github.com/Genisys33/CCNA/blob/master/TP6.md#bilan)
-   [Aller plus loin](https://github.com/Genisys33/CCNA/blob/master/TP6.md#aller-plus-loin)

# [](https://github.com/Genisys33/CCNA/blob/master/TP6.md#lab-1--simple-ospf)Lab 1 : Simple OSPF

Petit lab simple pour comprendre le concept.

# [](https://github.com/Genisys33/CCNA/blob/master/TP6.md#lab-2--un-peu-de-complexit%C3%A9-et-dutilit%C3%A9-)Lab 2 : Un peu de complexité (et d'utilité ?)

## [](https://github.com/Genisys33/CCNA/blob/master/TP6.md#1-pr%C3%A9sentation-du-lab-et-contexte)1. Présentation du lab et contexte

Notre labo aura cette forme; avec une infrastructure de 5 routeurs, 2 clients et 1 serveur)

[![maptp6](https://github.com/Genisys33/CCNA/raw/master/Image-Tp2/mapTP6.png)](https://github.com/Genisys33/CCNA/blob/master/Image-Tp2/mapTP6.png)

Nous avons fait nos addressages Ip suivant ce format :

Machines

`10.6.100.0/30`

`10.6.100.4/30`

`10.6.100.8/30`

`10.6.100.12/30`

`10.6.101.0/30`

`10.6.201.0/24`

`10.6.202.0/24`

Router1

`10.6.100.1`

`10.6.100.5`

xXx

xXx

xXx

xXx

`10.6.202.254`

Router2

`10.6.100.2`

xXx

`10.6.100.9`

xXx

xXx

xXx

xXx

Router3

xXx

xXx

`10.6.100.10`

`10.6.100.14`

`10.6.101.1`

xXx

xXx

Router4

xXx

`10.6.100.6`

xXx

`10.6.100.13`

xXx

xXx

xXx

Router5

xXx

xXx

xXx

xXx

`10.6.101.2`

`10.6.201.254`

xXx

Client1

xXx

xXx

xXx

xXx

xXx

`10.6.201.10`

xXx

Client2

xXx

xXx

xXx

xXx

xXx

`10.6.201.11`

xXx

Server

xXx

xXx

xXx

xXx

xXx

xXx

`10.6.202.10`

## [](https://github.com/Genisys33/CCNA/blob/master/TP6.md#2-mise-en-place-du-lab)2. Mise en place du lab

### [](https://github.com/Genisys33/CCNA/blob/master/TP6.md#checklist-ip-routeurs)Checklist IP Routeurs

Maintenant nous maitons en place les adresses ip de nos routeurs et leurs nom de domaine

Router1 :

  Router1#show ip int br
  Interface                  IP-Address      OK? Method Status                Protocol
  Ethernet0/0                10.6.202.254    YES NVRAM  up                    up      
  Ethernet0/1                10.6.100.1      YES NVRAM  up                    up      
  Ethernet0/2                10.6.100.5      YES NVRAM  up                    up      
  Ethernet0/3                unassigned      YES NVRAM  administratively down down    
  Router1#

Router2 :

  Router2#show ip int br 
  Interface                  IP-Address      OK? Method Status                Protocol
  Ethernet0/0                10.6.100.2      YES NVRAM  up                    up      
  Ethernet0/1                10.6.100.9      YES NVRAM  up                    up      
  Ethernet0/2                unassigned      YES NVRAM  administratively down down    
  Ethernet0/3                unassigned      YES NVRAM  administratively down down    
  Router2#

Router3 :

  Router3#show ip int br
  Interface                  IP-Address      OK? Method Status                Protocol
  Ethernet0/0                10.6.100.10     YES NVRAM  up                    up      
  Ethernet0/1                10.6.100.14     YES NVRAM  up                    up      
  Ethernet0/2                10.6.101.1      YES NVRAM  up                    up      
  Ethernet0/3                unassigned      YES NVRAM  administratively down down    
  Router3#

Router4 :

  Router4#show ip int br
  Interface                  IP-Address      OK? Method Status                Protocol
  Ethernet0/0                10.6.100.13     YES NVRAM  up                    up      
  Ethernet0/1                10.6.100.6      YES NVRAM  up                    up      
  Ethernet0/2                unassigned      YES NVRAM  administratively down down    
  Ethernet0/3                unassigned      YES NVRAM  administratively down down    
  Router4#

Router5 :

  Router5#show ip int br 
  Interface                  IP-Address      OK? Method Status                Protocol
  Ethernet0/0                10.6.101.2      YES NVRAM  up                    up      
  Ethernet0/1                10.6.201.254    YES NVRAM  up                    up      
  Ethernet0/2                unassigned      YES NVRAM  administratively down down    
  Ethernet0/3                unassigned      YES NVRAM  administratively down down    
  Router5#

### [](https://github.com/Genisys33/CCNA/blob/master/TP6.md#checklist-vms)Checklist VMs

Client1 :

  [root@client1 simon]# ip a && cat /etc/hosts
  2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
      link/ether 08:00:27:91:25:36 brd ff:ff:ff:ff:ff:ff
      inet 10.6.201.10/24 brd 10.6.201.255 scope global noprefixroute enp0s3
         valid_lft forever preferred_lft forever
      inet6 fe80::a00:27ff:fe91:2536/64 scope link 
         valid_lft forever preferred_lft forever
  127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
  ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
  10.6.201.11 client2
  10.6.202.10 server
  [root@client1 simon]# 

Client2 :

  [root@client2 simon]# ip a && cat /etc/hosts
  2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
      link/ether 08:00:27:21:57:cb brd ff:ff:ff:ff:ff:ff
      inet 10.6.201.11/24 brd 10.6.201.255 scope global noprefixroute enp0s3
         valid_lft forever preferred_lft forever
      inet6 fe80::a00:27ff:fe21:57cb/64 scope link 
         valid_lft forever preferred_lft forever
  127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
  ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
  10.6.201.10 client1
  10.6.202.10 server
  [root@client2 simon]#

Server :

 [root@serveur simon]# ip a && cat /etc/hosts
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:27:e9:d5 brd ff:ff:ff:ff:ff:ff
    inet 10.6.202.10/24 brd 10.6.202.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe27:e9d5/64 scope link 
       valid_lft forever preferred_lft forever
10.6.201.10 client1
10.6.201.11 client2
[root@server simon]#

### [](https://github.com/Genisys33/CCNA/blob/master/TP6.md#v%C3%A9rifier-que-tout-%C3%A7a-fonctionne)Vérifier que tout ça fonctionne

Router1 :

  Router1#show ip route
  Gateway of last resort is not set

       10.0.0.0/8 is variably subnetted, 7 subnets, 2 masks
  O       10.6.100.8/30 [110/20] via 10.6.100.2, 00:18:51, Ethernet0/1
  O       10.6.100.12/30 [110/20] via 10.6.100.6, 00:18:51, Ethernet0/2
  C       10.6.100.0/30 is directly connected, Ethernet0/1
  O IA    10.6.101.0/30 [110/30] via 10.6.100.6, 00:18:51, Ethernet0/2
                        [110/30] via 10.6.100.2, 00:18:51, Ethernet0/1
  C       10.6.100.4/30 is directly connected, Ethernet0/2
  O IA    10.6.201.0/24 [110/40] via 10.6.100.6, 00:18:45, Ethernet0/2
                        [110/40] via 10.6.100.2, 00:18:46, Ethernet0/1
  C       10.6.202.0/24 is directly connected, Ethernet0/0
  Router1#show ip protocols                                          
  Routing Protocol is "ospf 1"
    Outgoing update filter list for all interfaces is not set
    Incoming update filter list for all interfaces is not set
    Router ID 1.1.1.1
    It is an area border router
    Number of areas in this router is 2. 2 normal 0 stub 0 nssa
    Maximum path: 4
    Routing for Networks:
      10.6.100.0 0.0.0.3 area 0
      10.6.100.4 0.0.0.3 area 0
      10.6.202.0 0.0.0.255 area 2
   Reference bandwidth unit is 100 mbps
    Routing Information Sources:
      Gateway         Distance      Last Update
      4.4.4.4              110      00:21:32
      3.3.3.3              110      00:21:26
    Distance: (default is 110)

  Router1#show ip ospf neighbor

  Neighbor ID     Pri   State           Dead Time   Address         Interface
  4.4.4.4           1   FULL/DR         00:00:33    10.6.100.6      Ethernet0/2
  2.2.2.2           1   FULL/DR         00:00:33    10.6.100.2      Ethernet0/1
  Router1#

Router2 :

Router2#show ip route
Gateway of last resort is not set

     10.0.0.0/8 is variably subnetted, 7 subnets, 2 masks
C       10.6.100.8/30 is directly connected, Ethernet0/1
O       10.6.100.12/30 [110/20] via 10.6.100.10, 00:23:12, Ethernet0/1
C       10.6.100.0/30 is directly connected, Ethernet0/0
O IA    10.6.101.0/30 [110/20] via 10.6.100.10, 00:23:12, Ethernet0/1
O       10.6.100.4/30 [110/20] via 10.6.100.1, 00:23:12, Ethernet0/0
O IA    10.6.201.0/24 [110/30] via 10.6.100.10, 00:23:12, Ethernet0/1
O IA    10.6.202.0/24 [110/20] via 10.6.100.1, 00:23:14, Ethernet0/0
Router2#show ip protocols
Routing Protocol is "ospf 1"
  Outgoing update filter list for all interfaces is not set
  Incoming update filter list for all interfaces is not set
  Router ID 2.2.2.2
  Number of areas in this router is 1. 1 normal 0 stub 0 nssa
  Maximum path: 4
  Routing for Networks:
    10.6.100.0 0.0.0.3 area 0
    10.6.100.8 0.0.0.3 area 0
 Reference bandwidth unit is 100 mbps
  Routing Information Sources:
    Gateway         Distance      Last Update
    1.1.1.1              110      00:23:17
    4.4.4.4              110      00:23:17
    3.3.3.3              110      00:23:17
  Distance: (default is 110)

Router2#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
3.3.3.3           1   FULL/DR         00:00:38    10.6.100.10     Ethernet0/1
1.1.1.1           1   FULL/BDR        00:00:38    10.6.100.1      Ethernet0/0
Router2#

# [](https://github.com/Genisys33/CCNA/blob/master/TP6.md#lab-3--lets-end-this-properly)Lab 3 : Let's end this properly

# [](https://github.com/Genisys33/CCNA/blob/master/TP6.md#bilan)Bilan

# [](https://github.com/Genisys33/CCNA/blob/master/TP6.md#aller-plus-loin)Aller plus loin
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE0NDc0Nzc1NTIsLTIwODg3NDY2MTIsLT
E1MTkzMjM4NDldfQ==
-->