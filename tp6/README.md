    🌞 Dans le rendu, je veux

    ```
[tom@dns ~]$ sudo cat /etc/named.conf
[sudo] password for tom:
//
// named.conf
//
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching only nameserver (as a localhost DNS resolver only).
//
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//

options {
        listen-on port 53 { 127.0.0.1; any; };
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        secroots-file   "/var/named/data/named.secroots";
        recursing-file  "/var/named/data/named.recursing";
        allow-query     { localhost; any; };
        allow-query-cache { localhost; any; };


        /*
         - If you are building an AUTHORITATIVE DNS server, do NOT enable recursion.
         - If you are building a RECURSIVE (caching) DNS server, you need to enable
           recursion.
         - If your recursive DNS server has a public IP address, you MUST enable access
           control to limit queries to your legitimate users. Failing to do so will
           cause your server to become part of large scale DNS amplification
           attacks. Implementing BCP38 within your network would greatly
           reduce such attack surface
        */
        recursion yes;
        dnssec-validation yes;

        managed-keys-directory "/var/named/dynamic";
        geoip-directory "/usr/share/GeoIP";

        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";

        /* https://fedoraproject.org/wiki/Changes/CryptoPolicy */
        include "/etc/crypto-policies/back-ends/bind.config";
};

logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};

zone "." IN {
        type hint;
        file "named.ca";
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";

zone "tp6.b1" IN {
     type master;
     file "tp6.b1.db";
     allow-update { none; };
     allow-query {any; };
};
# référence vers notre fichier de zone inverse
zone "1.4.10.in-addr.arpa" IN {
     type master;
     file "tp6.b1.rev";
     allow-update { none; };
     allow-query { any; };
};
[tom@dns ~]$ sudo cat /var/named/tp6.b1.db
$TTL 86400
@ IN SOA dns.tp6.b1. admin.tp6.b1. (
    2019061800 ;Serial
    3600 ;Refresh
    1800 ;Retry
    604800 ;Expire
    86400 ;Minimum TTL
)

; Infos sur le serveur DNS lui même (NS = NameServer)
@ IN NS dns.tp6.b1.

; Enregistrements DNS pour faire correspondre des noms à des IPs
dns       IN A 10.6.1.101
john      IN A 10.6.1.11
[tom@dns ~]$ sudo cat /var/named/tp6.b1.rev
$TTL 86400
@ IN SOA dns.tp6.b1. admin.tp6.b1. (
    2019061800 ;Serial
    3600 ;Refresh
    1800 ;Retry
    604800 ;Expire
    86400 ;Minimum TTL
)

; Infos sur le serveur DNS lui même (NS = NameServer)
@ IN NS dns.tp6.b1.

; Reverse lookup
101 IN PTR dns.tp6.b1.
11 IN PTR john.tp6.b1.
[tom@dns ~]$ sudo systemctl status named
● named.service - Berkeley Internet Name Domain (DNS)
     Loaded: loaded (/usr/lib/systemd/system/named.service; enabled; preset>
     Active: active (running) since Fri 2023-11-17 11:17:39 CET; 6min ago
   Main PID: 41158 (named)
      Tasks: 5 (limit: 4672)
     Memory: 18.7M
        CPU: 143ms
     CGroup: /system.slice/named.service
             └─41158 /usr/sbin/named -u named -c /etc/named.conf

Nov 17 11:17:39 dns.tp6.b1 named[41158]: network unreachable resolving './N>
Nov 17 11:17:39 dns.tp6.b1 named[41158]: zone 1.0.0.127.in-addr.arpa/IN: lo>
Nov 17 11:17:39 dns.tp6.b1 named[41158]: zone 1.0.0.0.0.0.0.0.0.0.0.0.0.0.0>
Nov 17 11:17:39 dns.tp6.b1 named[41158]: zone localhost/IN: loaded serial 0
Nov 17 11:17:39 dns.tp6.b1 named[41158]: zone localhost.localdomain/IN: loa>
Nov 17 11:17:39 dns.tp6.b1 named[41158]: all zones loaded
Nov 17 11:17:39 dns.tp6.b1 systemd[1]: Started Berkeley Internet Name Domai>
Nov 17 11:17:40 dns.tp6.b1 named[41158]: running
Nov 17 11:17:40 dns.tp6.b1 named[41158]: managed-keys-zone: Initializing au>
Nov 17 11:17:40 dns.tp6.b1 named[41158]: resolver priming query complete
    ```

```
     [tom@dns ~]$ sudo ss -ltunp
Netid           State            Recv-Q           Send-Q                     Local Address:Port                     Peer Address:Port          Process
udp             UNCONN           0                0                              127.0.0.1:323                           0.0.0.0:*              users:(("chronyd",pid=657,fd=5))
udp             UNCONN           0                0                             10.6.1.101:53                            0.0.0.0:*              users:(("named",pid=41158,fd=19))
udp             UNCONN           0                0                              127.0.0.1:53                            0.0.0.0:*              users:(("named",pid=41158,fd=16))
udp             UNCONN           0                0                                  [::1]:323                              [::]:*              users:(("chronyd",pid=657,fd=6))
udp             UNCONN           0                0                                  [::1]:53                               [::]:*              users:(("named",pid=41158,fd=22))
tcp             LISTEN           0                10                            10.6.1.101:53                            0.0.0.0:*              users:(("named",pid=41158,fd=21))
tcp             LISTEN           0                10                             127.0.0.1:53                            0.0.0.0:*              users:(("named",pid=41158,fd=17))
tcp             LISTEN           0                128                              0.0.0.0:22                            0.0.0.0:*              users:(("sshd",pid=27208,fd=3))
tcp             LISTEN           0                4096                           127.0.0.1:953                           0.0.0.0:*              users:(("named",pid=41158,fd=24))
tcp             LISTEN           0                4096                               [::1]:953                              [::]:*              users:(("named",pid=41158,fd=25))
tcp             LISTEN           0                128                                 [::]:22                               [::]:*              users:(("sshd",pid=27208,fd=4))
tcp             LISTEN           0                10                                 [::1]:53                               [::]:*              users:(("named",pid=41158,fd=23))
```

🌞 Ouvrez le bon port dans le firewall
```
[tom@dns ~]$ ss -tulpn
Netid      State       Recv-Q      Send-Q            Local Address:Port             Peer Address:Port      Process
udp        UNCONN      0           0                    10.6.1.101:53                    0.0.0.0:*
udp        UNCONN      0           0                     127.0.0.1:53                    0.0.0.0:*
udp        UNCONN      0           0                     127.0.0.1:323                   0.0.0.0:*
udp        UNCONN      0           0                         [::1]:53                       [::]:*
udp        UNCONN      0           0                         [::1]:323                      [::]:*
tcp        LISTEN      0           4096                  127.0.0.1:953                   0.0.0.0:*
tcp        LISTEN      0           10                   10.6.1.101:53                    0.0.0.0:*
tcp        LISTEN      0           128                     0.0.0.0:22                    0.0.0.0:*
tcp        LISTEN      0           10                    127.0.0.1:53                    0.0.0.0:*
tcp        LISTEN      0           10                        [::1]:53                       [::]:*
tcp        LISTEN      0           4096                      [::1]:953                      [::]:*
tcp        LISTEN      0           128                        [::]:22                       [::]:*
```
```
[tom@dns ~]$  sudo firewall-cmd --add-port=53/udp --permanent
[sudo] password for tom:
success
[tom@dns ~]$  sudo firewall-cmd --reload
success
```

# 3. Test

        🌞 Sur la machine john.tp6.b1
```
[tom@john ~]$ ping john.tp6.b1
PING john.tp6.b1(john.tp6.b1 (fe80::a00:27ff:fe4d:f32e%enp0s3)) 56 data bytes
64 bytes from john.tp6.b1 (fe80::a00:27ff:fe4d:f32e%enp0s3): icmp_seq=1 ttl=64 time=0.278 ms
64 bytes from john.tp6.b1 (fe80::a00:27ff:fe4d:f32e%enp0s3): icmp_seq=2 ttl=64 time=0.066 ms
64 bytes from john.tp6.b1 (fe80::a00:27ff:fe4d:f32e%enp0s3): icmp_seq=3 ttl=64 time=0.219 ms
64 bytes from john.tp6.b1 (fe80::a00:27ff:fe4d:f32e%enp0s3): icmp_seq=4 ttl=64 time=0.083 ms
^C
--- john.tp6.b1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3008ms
rtt min/avg/max/mdev = 0.066/0.161/0.278/0.089 ms
```
```
[tom@john ~]$ ping dns.tp6.b1
PING dns.tp6.b1 (10.6.1.101) 56(84) bytes of data.
64 bytes from 10.6.1.101 (10.6.1.101): icmp_seq=1 ttl=64 time=2.65 ms
64 bytes from 10.6.1.101 (10.6.1.101): icmp_seq=2 ttl=64 time=2.28 ms
64 bytes from 10.6.1.101 (10.6.1.101): icmp_seq=3 ttl=64 time=1.58 ms
64 bytes from 10.6.1.101 (10.6.1.101): icmp_seq=4 ttl=64 time=1.85 ms
^C
--- dns.tp6.b1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3007ms
rtt min/avg/max/mdev = 1.577/2.091/2.650/0.409 ms
```
```
[tom@john ~]$ ping www.ynov.com
PING www.ynov.com (172.67.74.226) 56(84) bytes of data.
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=1 ttl=55 time=22.4 ms
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=2 ttl=55 time=24.6 ms
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=3 ttl=55 time=26.1 ms
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=4 ttl=55 time=25.0 ms
^C
--- www.ynov.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3004ms
rtt min/avg/max/mdev = 22.369/24.515/26.094/1.357 ms
```
