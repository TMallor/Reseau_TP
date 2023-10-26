# TP3 : On va router des trucs

 # I. ARP

 ```
[tom@localhost ~]$ cd /etc/sysconfig/network-scripts
[tom@localhost network-scripts]$ cat ifcfg-enp0s3
DEVICE=enp0s3

BOOTPROTO=static
ONBOOT=yes

(JOHN)IPADDR=10.3.1.11
(MARCEL)IPADDR=10.3.1.12
NETMASK=255.255.255.0
```

🌞Générer des requêtes ARP

°Effectuer un ping d'une machine à l'autre


```
    --- 10.3.1.12 ping statistics ---
90 packets transmitted, 90 received, 0% packet loss, time 89233ms
rtt min/avg/max/mdev = 0.832/2.138/9.221/1.274 ms

--- 10.3.1.11 ping statistics ---
144 packets transmitted, 144 received, 0% packet loss, time 143363ms
rtt min/avg/max/mdev = 0.950/2.090/5.214/0.901 ms

```
°Repérer l'adresse MAC de john dans la table ARP de marcel et vice-versa

```
[tom@localhost ~]$ ip neigh show
10.3.1.11 dev enp0s3 lladdr 08:00:27:18:d2:1d REACHABLE
```

-Prouvez que l'info est correcte (que l'adresse MAC que vous voyez dans la table est bien celle de la machine correspondante)
    
    °une commande pour voir la MAC de marcel dans la table ARP de john
        -[tom@localhost ~]$ ip neigh show
            10.3.1.12 dev enp0s3 lladdr 08:00:27:bf:11:65 REACHABLE
    Et une commande pour afficher la MAC de marcel, depuis marcel
        -[tom@localhost ~]$ ip a
            08:00:27:bf:11:65

🌞Analyse de trames

```
 [tom@localhost ~]$ ping 10.3.1.12
PING 10.3.1.12 (10.3.1.12) 56(84) bytes of data.
64 bytes from 10.3.1.12: icmp_seq=1 ttl=64 time=6.00 ms
64 bytes from 10.3.1.12: icmp_seq=2 ttl=64 time=0.941 ms
64 bytes from 10.3.1.12: icmp_seq=3 ttl=64 time=2.05 ms
64 bytes from 10.3.1.12: icmp_seq=4 ttl=64 time=1.67 ms
64 bytes from 10.3.1.12: icmp_seq=5 ttl=64 time=1.56 ms
--- 10.3.1.12 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4027ms
rtt min/avg/max/mdev = 0.941/2.443/5.997/1.812 ms
```
  
🦈    voir toto.pcap 


## II. Routage

JOHN :
```
[tom@localhost network-scripts]$ cat ifcfg-enp0s3
DEVICE=enp0s3

BOOTPROTO=static
ONBOOT=yes

IPADDR=10.3.1.11
NETMASK=255.255.255.0
```

MARCEL :
```
[tom@localhost network-scripts]$ cat ifcfg-enp0s3
DEVICE=enp0s3

BOOTPROTO=static
ONBOOT=yes

IPADDR=10.3.1.12
NETMASK=255.255.255.0
```

ROUTEUR :
```
[tom@localhost network-scripts]$ cat ifcfg-enp0s3
DEVICE=enp0s3

BOOTPROTO=static
ONBOOT=yes

IPADDR=10.3.1.254
NETMASK=255.255.255.0
```

```
[tom@localhost network-scripts]$ cat ifcfg-enp0s8
DEVICE=enp0s3

BOOTPROTO=static
ONBOOT=yes

IPADDR=10.3.2.254
NETMASK=255.255.255.0
```

---

Test ping :
```
--- 10.3.1.254 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4009ms
rtt min/avg/max/mdev = 1.783/2.073/2.499/0.237 ms
```

```
--- 10.3.2.254 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3010ms
rtt min/avg/max/mdev = 1.514/1.835/2.215/0.317 ms
```

### 1. Mise en place du routage

🌞Ajouter les routes statiques nécessaires pour que john et marcel puissent se ping

Pour John :
```
 sudo ip route add 10.3.2.0/24 via 10.3.1.254 dev enp0s3
---
[tom@localhost network-scripts]$ ip r s
10.3.1.0/24 dev enp0s3 proto kernel scope link src 10.3.1.11 metric 100
10.3.2.0/24 via 10.3.1.254 dev enp0s3
```
Pour Marcel :
```
 sudo ip route add 10.3.1.0/24 via 10.3.2.254 dev enp0s3
 ---
 [tom@localhost network-scripts]$ ip route
 show
10.3.1.0/24 via 10.3.2.254 dev enp0s3
10.3.2.0/24 dev enp0s3 proto kernel scope link src 10.3.2.12 metric 100
```
### 2. Analyse de trames

🌞Analyse des échanges ARP

```
sudo ip neigh flush all
```
| ordre | type trame  | IP source | MAC source                | IP destination | MAC destination            |
| ----- | ----------- | --------- | ------------------------- | -------------- | -------------------------- |
| 1     | Requête ARP | x         |`marcel` `08:00:27:BF:11:65`| x             | Broadcast `FF:FF:FF:FF:FF` |
| 2     | Réponse ARP | x         |`john` `08:00:27:18:D2:1D` | x              |`marcel` `08:00:27:BF:11:65`|
| ...   | ...         | ...       |...                        |                |                            |
| 3     | Ping        | 10.3.1.12 |`marcel` `08:00:27:BF:11:65`| 10.3.1.11     |`john` `08:00:27:18:D2:1D`  |
| 4     | Pong        | 10.3.1.11 |`john` `08:00:27:18:D2:1D` | 10.3.1.12      |`marcel` `08:00:27:BF:11:65`|

### 3. Accès internet
🌞Donnez un accès internet à vos machines - config routeur

Vérification du NAT: