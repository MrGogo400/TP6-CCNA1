
# B1 Réseau 2019 - TP6

## Sommaire
* [Lab 1 : Simple OSPF](#lab-1--simple-ospf)
* [Lab 2 : Un peu de complexité (et d'utilité ?)](#lab-2--un-peu-de-complexité-et-dutilité-)
  * [1. Présentation du lab et contexte](#1-présentation-du-lab-et-contexte)
    * [Schéma de la topologie](#schéma-de-la-topologie)
    * [Aires OSPF](#aires-ospf)
    * [Réseaux IP et aires OSPF](#réseaux-ip-et-aires-ospf)
    * [Adressage IP des machines](#adressage-ip-de-chacune-des-machines)
  * [2. Mise en place du lab](#2-mise-en-place-du-lab)
    * [Adressage statique - routeurs](#checklist-ip-routeurs)
    * [Adressage statique - VMs](#checklist-vms)
    * [OSPF](#configuration-de-ospf)
* [Lab 3 : Let's end this properly](#lab-3--lets-end-this-properly)
  * [NAT (accès internet dans tout le réseau)](#1-nat--accès-internet)
  * [Un service d'infra](#2-un-service-dinfra)
  * [DHCP (adressage dynamique pour les clients)](#3-serveur-dhcp)
  * [DNS](#4-serveur-dns)
  * [NTP (synchronisation de l'heure)](#5-serveur-ntp)
* [Bilan](#bilan)
* [Aller plus loin](#aller-plus-loin)

# Lab 1 : Simple OSPF

Petit lab simple pour comprendre le concept.

# Lab 2 : Un peu de complexité (et d'utilité ?)

## 1. Présentation du lab et contexte

Notre labo aura cette forme; avec une infrastructure de 5 routeurs, 2 clients et 1 serveur)

![maptp6](https://github.com/Genisys33/CCNA/blob/master/Image-Tp2/mapTP6.png)

Nous avons fait nos addressages Ip suivant ce format : 

Machines | `10.6.100.0/30` | `10.6.100.4/30` | `10.6.100.8/30` | `10.6.100.12/30` | `10.6.101.0/30` | `10.6.201.0/24` | `10.6.202.0/24`
--- | --- | --- | --- | --- | --- | --- | --- 
Router1 | `10.6.100.1` | `10.6.100.5` | xXx | xXx | xXx | xXx | `10.6.202.254`
Router2 | `10.6.100.2` | xXx |  `10.6.100.9` | xXx | xXx | xXx | xXx
Router3 | xXx | xXx | `10.6.100.10` | `10.6.100.14` | `10.6.101.1` | xXx | xXx
Router4 | xXx |  `10.6.100.6` | xXx | `10.6.100.13` | xXx | xXx | xXx
Router5 | xXx | xXx | xXx | xXx |  `10.6.101.2` |  `10.6.201.254` | xXx
Client1 | xXx | xXx | xXx | xXx | xXx |  `10.6.201.10` | xXx
Client2 | xXx | xXx | xXx | xXx | xXx |  `10.6.201.11` | xXx
Server | xXx | xXx | xXx | xXx | xXx | xXx | `10.6.202.10`

## 2. Mise en place du lab

### Checklist IP Routeurs

Maintenant nous maitons en place les adresses ip de nos routeurs et leurs nom de domaine 

Router1 :
```bash
  Router1#show ip int br
  Interface                  IP-Address      OK? Method Status                Protocol
  Ethernet0/0                10.6.202.254    YES NVRAM  up                    up      
  Ethernet0/1                10.6.100.1      YES NVRAM  up                    up      
  Ethernet0/2                10.6.100.5      YES NVRAM  up                    up      
  Ethernet0/3                unassigned      YES NVRAM  administratively down down    
  Router1#
```
Router2 :
```bash
  Router2#show ip int br 
  Interface                  IP-Address      OK? Method Status                Protocol
  Ethernet0/0                10.6.100.2      YES NVRAM  up                    up      
  Ethernet0/1                10.6.100.9      YES NVRAM  up                    up      
  Ethernet0/2                unassigned      YES NVRAM  administratively down down    
  Ethernet0/3                unassigned      YES NVRAM  administratively down down    
  Router2#
```

Router3 :
```bash
  Router3#show ip int br
  Interface                  IP-Address      OK? Method Status                Protocol
  Ethernet0/0                10.6.100.10     YES NVRAM  up                    up      
  Ethernet0/1                10.6.100.14     YES NVRAM  up                    up      
  Ethernet0/2                10.6.101.1      YES NVRAM  up                    up      
  Ethernet0/3                unassigned      YES NVRAM  administratively down down    
  Router3#
``` 

Router4 :
```bash
  Router4#show ip int br
  Interface                  IP-Address      OK? Method Status                Protocol
  Ethernet0/0                10.6.100.13     YES NVRAM  up                    up      
  Ethernet0/1                10.6.100.6      YES NVRAM  up                    up      
  Ethernet0/2                unassigned      YES NVRAM  administratively down down    
  Ethernet0/3                unassigned      YES NVRAM  administratively down down    
  Router4#
```

Router5 :
```bash
  Router5#show ip int br 
  Interface                  IP-Address      OK? Method Status                Protocol
  Ethernet0/0                10.6.101.2      YES NVRAM  up                    up      
  Ethernet0/1                10.6.201.254    YES NVRAM  up                    up      
  Ethernet0/2                unassigned      YES NVRAM  administratively down down    
  Ethernet0/3                unassigned      YES NVRAM  administratively down down    
  Router5#
```

### Checklist VMs

Client1 :
```bash
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
```
Client2 :
```bash
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
``` 
Server :
  ```bash
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
```

### Vérifier que tout ça fonctionne

Nous allons maintenant configurer correctement l'ospf qui se chargera de nos routes entre les routeurs.

Router1 :
```bash
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
``` 
Router2 :
```bash
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
```
Router3 :
```bash
  Router3#show ip route
  Gateway of last resort is not set

       10.0.0.0/8 is variably subnetted, 7 subnets, 2 masks
  C       10.6.100.8/30 is directly connected, Ethernet0/0
  C       10.6.100.12/30 is directly connected, Ethernet0/1
  O       10.6.100.0/30 [110/20] via 10.6.100.9, 00:25:16, Ethernet0/0
  C       10.6.101.0/30 is directly connected, Ethernet0/2
  O       10.6.100.4/30 [110/20] via 10.6.100.13, 00:25:16, Ethernet0/1
  O       10.6.201.0/24 [110/20] via 10.6.101.2, 00:25:16, Ethernet0/2
  O IA    10.6.202.0/24 [110/30] via 10.6.100.13, 00:25:16, Ethernet0/1
                        [110/30] via 10.6.100.9, 00:25:17, Ethernet0/0
  Router3#show ip protocols
  Routing Protocol is "ospf 1"
    Outgoing update filter list for all interfaces is not set
    Incoming update filter list for all interfaces is not set
    Router ID 3.3.3.3
    It is an area border router
    Number of areas in this router is 2. 2 normal 0 stub 0 nssa
    Maximum path: 4
    Routing for Networks:
      10.6.100.8 0.0.0.3 area 0
      10.6.100.12 0.0.0.3 area 0
      10.6.101.0 0.0.0.3 area 1
   Reference bandwidth unit is 100 mbps
    Routing Information Sources:
      Gateway         Distance      Last Update
      1.1.1.1              110      00:25:21
      4.4.4.4              110      00:25:21
      5.5.5.5              110      00:25:21
      2.2.2.2              110      00:25:21
    Distance: (default is 110)

  Router3#show ip ospf neighbor

  Neighbor ID     Pri   State           Dead Time   Address         Interface
  4.4.4.4           1   FULL/DR         00:00:30    10.6.100.13     Ethernet0/1
  2.2.2.2           1   FULL/BDR        00:00:31    10.6.100.9      Ethernet0/0
  5.5.5.5           1   FULL/DR         00:00:31    10.6.101.2      Ethernet0/2
  Router3#
```
Router4 :
```bash
  Router4#show ip route
  Gateway of last resort is not set
       10.0.0.0/8 is variably subnetted, 7 subnets, 2 masks
  O       10.6.100.8/30 [110/20] via 10.6.100.14, 00:26:33, Ethernet0/0
  C       10.6.100.12/30 is directly connected, Ethernet0/0
  O       10.6.100.0/30 [110/20] via 10.6.100.5, 00:26:33, Ethernet0/1
  O IA    10.6.101.0/30 [110/20] via 10.6.100.14, 00:26:33, Ethernet0/0
  C       10.6.100.4/30 is directly connected, Ethernet0/1
  O IA    10.6.201.0/24 [110/30] via 10.6.100.14, 00:26:28, Ethernet0/0
  O IA    10.6.202.0/24 [110/20] via 10.6.100.5, 00:26:34, Ethernet0/1
  Router4#show ip route
  Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
         D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
         N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
         E1 - OSPF external type 1, E2 - OSPF external type 2
         i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
         ia - IS-IS inter area, * - candidate default, U - per-user static route
         o - ODR, P - periodic downloaded static route

  Gateway of last resort is not set

       10.0.0.0/8 is variably subnetted, 7 subnets, 2 masks
  O       10.6.100.8/30 [110/20] via 10.6.100.14, 00:26:37, Ethernet0/0
  C       10.6.100.12/30 is directly connected, Ethernet0/0
  O       10.6.100.0/30 [110/20] via 10.6.100.5, 00:26:37, Ethernet0/1
  O IA    10.6.101.0/30 [110/20] via 10.6.100.14, 00:26:37, Ethernet0/0
  C       10.6.100.4/30 is directly connected, Ethernet0/1
  O IA    10.6.201.0/24 [110/30] via 10.6.100.14, 00:26:31, Ethernet0/0
  O IA    10.6.202.0/24 [110/20] via 10.6.100.5, 00:26:38, Ethernet0/1
  Router4#show ip ospf neighbor

  Neighbor ID     Pri   State           Dead Time   Address         Interface
  3.3.3.3           1   FULL/BDR        00:00:39    10.6.100.14     Ethernet0/0
  1.1.1.1           1   FULL/BDR        00:00:39    10.6.100.5      Ethernet0/1
  Router4#
```

Router5 :
```bash
  Router5#show ip route
  Gateway of last resort is not set

       10.0.0.0/8 is variably subnetted, 7 subnets, 2 masks
  O IA    10.6.100.8/30 [110/20] via 10.6.101.1, 00:28:01, Ethernet0/0
  O IA    10.6.100.12/30 [110/20] via 10.6.101.1, 00:28:01, Ethernet0/0
  O IA    10.6.100.0/30 [110/30] via 10.6.101.1, 00:28:01, Ethernet0/0
  C       10.6.101.0/30 is directly connected, Ethernet0/0
  O IA    10.6.100.4/30 [110/30] via 10.6.101.1, 00:27:56, Ethernet0/0
  C       10.6.201.0/24 is directly connected, Ethernet0/1
  O IA    10.6.202.0/24 [110/40] via 10.6.101.1, 00:27:56, Ethernet0/0
  Router5#show ip protocols
  Routing Protocol is "ospf 1"
    Outgoing update filter list for all interfaces is not set
    Incoming update filter list for all interfaces is not set
    Router ID 5.5.5.5
    Number of areas in this router is 1. 1 normal 0 stub 0 nssa
    Maximum path: 4
    Routing for Networks:
      10.6.101.0 0.0.0.3 area 1
      10.6.201.0 0.0.0.255 area 1
   Reference bandwidth unit is 100 mbps
    Routing Information Sources:
      Gateway         Distance      Last Update
      3.3.3.3              110      00:28:02
    Distance: (default is 110)
  Router5#show ip ospf neighbor

  Neighbor ID     Pri   State           Dead Time   Address         Interface
  3.3.3.3           1   FULL/BDR        00:00:35    10.6.101.1      Ethernet0/0
  Router5#
```
Nous allons ping le server avec les clients et l'inverse. 
Router1 :
```bash
[root@client1 simon]# ping -c 4 server
PING server (10.6.202.10) 56(84) bytes of data.
64 bytes from server (10.6.202.10): icmp_seq=2 ttl=60 time=53.6 ms
64 bytes from server (10.6.202.10): icmp_seq=3 ttl=60 time=59.0 ms
64 bytes from server (10.6.202.10): icmp_seq=4 ttl=60 time=48.1 ms

--- server ping statistics ---
4 packets transmitted, 3 received, 25% packet loss, time 3007ms
rtt min/avg/max/mdev = 48.175/53.603/59.030/4.431 ms
[root@client1 simon]# 
```

Router2 :
```bash
root@client2 simon]# ping -c 4 server 
PING server (10.6.202.10) 56(84) bytes of data.
64 bytes from server (10.6.202.10): icmp_seq=1 ttl=60 time=56.5 ms
64 bytes from server (10.6.202.10): icmp_seq=2 ttl=60 time=49.9 ms
64 bytes from server (10.6.202.10): icmp_seq=3 ttl=60 time=47.5 ms
64 bytes from server (10.6.202.10): icmp_seq=4 ttl=60 time=41.9 ms

--- server ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3011ms
rtt min/avg/max/mdev = 41.941/48.990/56.581/5.255 ms
[root@client2 simon]# 
```

Server :
```bash
  [root@serveur simon]# ping -c 4 client1 && ping -c 4 client2
  PING client1 (10.6.201.10) 56(84) bytes of data.
  64 bytes from client1 (10.6.201.10): icmp_seq=1 ttl=60 time=48.8 ms
  64 bytes from client1 (10.6.201.10): icmp_seq=2 ttl=60 time=46.0 ms
  64 bytes from client1 (10.6.201.10): icmp_seq=3 ttl=60 time=43.0 ms
  64 bytes from client1 (10.6.201.10): icmp_seq=4 ttl=60 time=61.3 ms

  --- client1 ping statistics ---
  4 packets transmitted, 4 received, 0% packet loss, time 3003ms
  rtt min/avg/max/mdev = 43.084/49.829/61.358/6.961 ms
  PING client2 (10.6.201.11) 56(84) bytes of data.
  64 bytes from client2 (10.6.201.11): icmp_seq=1 ttl=60 time=45.9 ms
  64 bytes from client2 (10.6.201.11): icmp_seq=2 ttl=60 time=44.8 ms
  64 bytes from client2 (10.6.201.11): icmp_seq=3 ttl=60 time=61.2 ms
  64 bytes from client2 (10.6.201.11): icmp_seq=4 ttl=60 time=57.8 ms

  --- client2 ping statistics ---
  4 packets transmitted, 4 received, 0% packet loss, time 3003ms
  rtt min/avg/max/mdev = 44.851/52.492/61.233/7.181 ms
  [root@serveur simon]#
```

# Lab 3 : Let's end this properly

# Bilan

# Aller plus loin
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTg4Nzc4OTQ0LC0xNDQ3NDc3NTUyLC0yMD
g4NzQ2NjEyLC0xNTE5MzIzODQ5XX0=
-->