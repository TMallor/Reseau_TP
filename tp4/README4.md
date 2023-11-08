   # I. DHCP Client

🌞 Déterminer  
   
 ``` 
   Bail obtenu. . . . . . . . . . . . . . : jeudi 2 novembre 2023 15:10:28
   Bail expirant. . . . . . . . . . . . . : vendredi 3 novembre 2023 15:10:28
   Passerelle par défaut. . . . . . . . . : 172.20.10.1
   Serveur DHCP . . . . . . . . . . . . . : 172.20.10.1
```

🌞 Analyser la capture Wireshark

la trame dhcp contenant offer dans la colonne "info" est celle qui contient les informations proposées au client. 

   # II. Serveur DHCP

   🌞 Preuve de mise en place
```
[tom@dhcp ~]$ ping google.com
PING google.com (172.217.18.206) 56(84) bytes of data.
64 bytes from ham02s14-in-f206.1e100.net (172.217.18.206): icmp_seq=1 ttl=115 time=18.1 ms
^C64 bytes from 172.217.18.206: icmp_seq=2 ttl=115 time=19.2 ms

--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 5043ms
rtt min/avg/max/mdev = 18.137/18.681/19.226/0.544 ms
```

```
   [tom@node2 ~]$ ping google.com
PING google.com (172.217.18.206) 56(84) bytes of data.
64 bytes from ham02s14-in-f206.1e100.net (172.217.18.206): icmp_seq=1 ttl=115 time=18.4 ms
^C64 bytes from 172.217.18.206: icmp_seq=2 ttl=115 time=17.6 ms

--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 5045ms
rtt min/avg/max/mdev = 17.594/18.015/18.436/0.421 ms
[tom@node2 ~]$ traceroute router.tp4.b1
^C
[tom@node2 ~]$ traceroute google.com
traceroute to google.com (172.217.18.206), 30 hops max, 60 byte packets
```

🌞 Rendu

```
[tom@dhcp ~] nano dnf -y install dhcp-server
[tom@dhcp ~] sudo nano /etc/dhcp/dhcpd.conf
```
```
#
# DHCP Server Configuration file. see /usr/share/doc/dhcp-server/dhcpd.conf.example see dhcpd.conf(5) man page
#
# create new
# specify domain name
option domain-name     "srv.world";
# specify DNS server's hostname or IP address
option domain-name-servers     dlp.srv.world;
# default lease time
default-lease-time 600;
# max lease time
max-lease-time 7200;
# this DHCP server to be declared valid
authoritative;
# specify network address and subnetmask
subnet 10.4.1.0 netmask 255.255.255.0 {
    # specify the range of lease IP address
    range dynamic-bootp 10.4.1.137 10.4.1.237;
    # specify broadcast address
    option broadcast-address 10.4.1.255;
    # specify gateway
    option routers 10.4.1.1;
}

```
```
● dhcpd.service - DHCPv4 Server Daemon
     Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; enabled; preset: disabled)
     Active: active (running) since Wed 2023-11-08 17:53:42 CET; 18s ago
       Docs: man:dhcpd(8)
             man:dhcpd.conf(5)
   Main PID: 1476 (dhcpd)
     Status: "Dispatching packets..."
      Tasks: 1 (limit: 4672)
     Memory: 4.6M
        CPU: 15ms
     CGroup: /system.slice/dhcpd.service
             └─1476 /usr/sbin/dhcpd -f -cf /etc/dhcp/dhcpd.conf -user dhcpd -group dhcpd --no-pid
```

# 5. Client DHCP

🌞 Test !
```
    sudo journalctl -xe -u dhcpd -f 
Nov 08 18:09:04 dhcp.tp4.b1 dhcpd[1476]: DHCPREQUEST for 10.4.1.3 from 08:00:27:ba:4b:bc via enp0s3: unknown lease 10.4.1.3.
Nov 08 18:18:48 dhcp.tp4.b1 dhcpd[1476]: DHCPREQUEST for 10.4.1.3 from 08:00:27:ba:4b:bc via enp0s3: unknown lease 10.4.1.3.
Nov 08 18:19:20 dhcp.tp4.b1 dhcpd[1476]: DHCPREQUEST for 10.4.1.3 from 08:00:27:ba:4b:bc via enp0s3: unknown lease 10.4.1.3.

```

🌞 Prouvez que

```
[tom@node1 ~]$ sudo nmcli con show enp0s3
connection.timestamp:                   1699464252
Mer 08/11/2023 18:24:12
DHCP4.OPTION[4]:                        expiry = 1699465752
Mer 08/11/2023 18:49:12
DHCP4.OPTION[3]:                        dhcp_server_identifier = 10.4.1.2

routeur :
[tom@node1 ~] ping 10.4.1.254
PING 10.4.1.254 (10.4.1.254) 56(84) bytes of data.
64 bytes from 10.4.1.254: icmp_seq=1 ttl=64 time=0.578 ms
64 bytes from 10.4.1.254: icmp_seq=2 ttl=64 time=0.901 ms
^C
--- 10.4.1.254 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1000ms


node2
[tom@node1 ~]ping 10.4.1.12
PING 10.4.1.12 (10.4.1.12) 56(84) bytes of data.
64 bytes from 10.4.1.12: icmp_seq=1 ttl=64 time=1.21 ms
64 bytes from 10.4.1.12: icmp_seq=2 ttl=64 time=0.882 ms
64 bytes from 10.4.1.12: icmp_seq=3 ttl=64 time=0.860 ms
^C
--- 10.4.1.12 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
```

[tom@dhcp ~]$  cat /var/lib/dhcpd/dhcpd.leases
# The format of this file is documented in the dhcpd.leases(5) manual page.
# This lease file was written by isc-dhcp-4.4.2b1

# authoring-byte-order entry is generated, DO NOT DELETE
authoring-byte-order little-endian;


🌞 Bail DHCP serveur

```
[tom@dhcp ~]$  cat /var/lib/dhcpd/dhcpd.leases
The format of this file is documented in the dhcpd.leases(5) manual page.
This lease file was written by isc-dhcp-4.4.2b1
authoring-byte-order entry is generated, DO NOT DELETE
authoring-byte-order little-endian;

server-duid "\000\001\000\001,\336y\226\010\000'\241\277\213";
```

# 6. Options DHCP

🌞 Nouvelle conf !
```
[tom@dhcp etc]$ sudo cat dhcp/dhcpd.conf
#
# DHCP Server Configuration file. see /usr/share/doc/dhcp-server/dhcpd.conf.example see dhcpd.conf(5) man page
#
# create new
# specify domain name
option domain-name     "srv.world";
# specify DNS server's hostname or IP address
option domain-name-servers     8.8.8.8;
# default lease time
default-lease-time 600;
# max lease time
max-lease-time 7200;
# this DHCP server to be declared valid
authoritative;
# specify network address and subnetmask
subnet 10.4.1.0 netmask 255.255.255.0 {
    # specify the range of lease IP address
    range dynamic-bootp 10.4.1.137 10.4.1.237;
    # specify broadcast address
    option broadcast-address 10.4.1.255;
    # specify gateway
    option routers 10.4.1.254;
}

[tom@dhcp etc]$ sudo systemctl restart dhcpd
```

🌞 Test !

      redemandez une IP avec le client node1.tp4.b1
```
sudo dhclient -r enp0s3
sudo dhclient enp0s3
```
prouvez-que :
         vous avez enregistré l'adresse d'un serveur DNS
```
[tom@dhcp etc]$ sudo cat resolv.conf
# Generated by NetworkManager
nameserver 10.33.10.2
nameserver 8.8.8.8
```
vous avez une nouvelle route par défaut qui a été récupérée dynamiquement

```
[tom@node1 ~]$ ip r s
10.4.1.0/24 dev enp0s3 proto kernel scope link src 10.4.1.3 metric 100
```
la durée de votre bail DHCP est bien de 6 heures
```
[slayz@node1 dhclient]$ cd var/lib/dhclient

[slayz@node1 dhclient]$ cat dhclient.leases
lease {
  interface "enp0s3";
  fixed-address 10.4.1.138;
  option subnet-mask 255.255.255.0;
  option routers 10.4.1.254;
  option dhcp-lease-time 21600;
  option dhcp-message-type 5;
  option domain-name-servers 8.8.8.8;
  option dhcp-server-identifier 10.4.1.253;
  option broadcast-address 10.4.1.255;
  option domain-name "srv.world";
  renew 5 2023/11/08 19:20:04;
  rebind 5 2023/10/08 19:20:04;
  expire 5 2023/11/08 19:20:04;
prouvez que vous avez un accès Internet après cet échange DHCP
[slayz@node1 ~]$ ping google.com
PING google.com (172.217.18.206) 56(84) bytes of data.
64 bytes from 172.217.18.206 (172.217.18.206): icmp_seq=1 ttl=54 time=32.8 ms
64 bytes from 172.217.18.206 (172.217.18.206): icmp_seq=2 ttl=54 time=31.3 ms
64 bytes from 172.217.18.206 (172.217.18.206): icmp_seq=3 ttl=54 time=32.3 ms
--- google.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 31.345/32.143/32.766/0.593 ms
```