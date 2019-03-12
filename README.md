TP6-CCNA1

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

### 1. Présentation du lab et contexte

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

Maintenant nous maitons en place les adresses ip de nos routeurs et leurs nom de domaine 

Router1:

```bash
Router1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                10.6.202.254    YES NVRAM  up                    up      
Ethernet0/1                10.6.100.1      YES NVRAM  up                    up      
Ethernet0/2                10.6.100.5      YES NVRAM  up                    up      
Ethernet0/3                unassigned      YES NVRAM  administratively down down    
Router1#
```
Router2:
```bash
Router2#show ip int br 
Interface                  IP-Address      OK? Method Status                Protocol
Ethernet0/0                10.6.100.2      YES NVRAM  up                    up      
Ethernet0/1                10.6.100.9      YES NVRAM  up                    up      
Ethernet0/2                unassigned      YES NVRAM  administratively down down    
Ethernet0/3                unassigned      YES NVRAM  administratively down down    
Router2#
```

Router3:
```bash


# Lab 3 : Let's end this properly

# Bilan

# Aller plus loin
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE5MTQ4MTgzMzddfQ==
-->