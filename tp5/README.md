 # TP5 : TCP, UDP et services réseau

 # I. First steps

    🌞 Déterminez, pour ces 5 applications, si c'est du TCP ou de l'UDP*

        

```
        Pour google.com
    Tcp
ip : 10.33.70.194
port :51088  vers  443

        Pour steam :
        Tcp
ip :  23.54.132.127
port : 51205 vers 443
 
    
        Pour Apple Music :  
        Tcp
ip : 23.192.137.197
port : 51319  vers 443

        Pour discord :
        Tcp
ip : 204.79.197.222
port :51398 vers 443

        Pour visual studio code :
        Tcp
ip : 23.192.137.197
port :51416 vers 443
``` 

🌞 Demandez l'avis à votre OS

```
 [chrome.exe]
  TCP    10.33.70.194:51451     34.120.22.49:443       ESTABLISHED

  [Steam.exe]
  TCP    127.0.0.1:51474        127.0.0.1:51483        ESTABLISHED

  [AppleMusic.exe]
  TCP    10.33.70.194:51529     150.171.22.254:443     ESTABLISHED

  [Discord.exe]
  TCP    10.33.70.194:51571     162.159.136.234:443    ESTABLISHED
```

 # II. Setup Virtuel
 # 1. SSH

🌞 Examinez le trafic dans Wireshark

```
        °déterminez si SSH utilise TCP ou UDP :

TCP
        °repérez le 3-Way Handshake à l'établissement de la connexion :

3	12.596480	10.5.1.1	10.5.1.11	TCP	66	50159 → 22 [SYN] Seq=0 Win=64240 Len=0 MSS=1460 WS=256 SACK_PERM
4	12.597139	10.5.1.11	10.5.1.1	TCP	66	22 → 50159 [SYN, ACK] Seq=0 Ack=1 Win=64240 Len=0 MSS=1460  SACK_PERM WS=128
7	12.603482	10.5.1.11	10.5.1.1	TCP	60	22 → 50159 [ACK] Seq=1 Ack=22 Win=64256 Len=0

        °repérez du trafic SSH: 
17	12.707189	10.5.1.1	10.5.1.11	SSHv2	98	Client: 

        °repérez le FIN ACK à la fin d'une connexion :

58	18.075231	10.5.1.1	10.5.1.11	TCP	54	50159 → 22 [FIN, ACK] Seq=2534 Ack=2998 Win=262656 Len=0


```

🌞 
```
[tom@node1 ~]$ ss -tpn
State        Recv-Q        Send-Q               Local Address:Port               Peer Address:Port        Process
ESTAB        0             52                       10.5.1.11:22                     10.5.1.1:50452

PS C:\WINDOWS\system32> netstat -n -b | Select-String "ssh" -Context 1,0

    TCP    10.5.1.1:50462         10.5.1.11:22           ESTABLISHED
>  [ssh.exe]

```

# 2. Routage
        🌞 Prouvez que

```
[tom@node1 ~]$ sudo ip route add default via 10.5.1.254 dev enp0s3
[sudo] password for tom:
[tom@node1 ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=112 time=20.1 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=112 time=21.2 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=112 time=20.7 ms
^C
--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
rtt min/avg/max/mdev = 20.120/20.667/21.215/0.447 ms
```

# 3. Serveur Web
                🌞 Installez le paquet nginx
```
[tom@web ~]$ sudo dnf install nginx -y
Last metadata expiration check: 0:00:41 ago on Tue 14 Nov 2023 11:44:53 CET.
Package nginx-1:1.20.1-14.el9_2.1.x86_64 is already installed.
Dependencies resolved.
Nothing to do.
Complete!
```
        🌞 Créer le site web

```
[tom@web ~]$ sudo mkdir -p /var/www/site_web_nul
[tom@web ~]$ cd /var/www/site_web_nul/
[tom@web site_web_nul]$ sudo nano index.html
[tom@web site_web_nul]$ cat index.html
<h1>MEOW</h1>
```
        🌞 Donner les bonnes permissions

```
[tom@web site_web_nul]$ sudo chown -R nginx:nginx /var/www/site_web_nul

```

        🌞 Créer un fichier de configuration NGINX pour notre site web

```
[tom@web conf.d]$ sudo nano site_web_nul.conf
[sudo] password for tom:
[tom@web conf.d]$ cat site_web_nul.conf
server {
  listen 80;
  index index.html;
  server_name www.site_web_nul.b1
  root /var/www/site_web_nul;
}
```

🌞 Démarrer le serveur web !

```
[tom@web conf.d]$ sudo systemctl start nginx
[tom@web conf.d]$  
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; preset: disabled)
     Active: active (running) since Tue 2023-11-14 20:41:25 CET; 5min ago
    Process: 1445 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
    Process: 1446 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
    Process: 1447 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
   Main PID: 1448 (nginx)
      Tasks: 2 (limit: 4672)
     Memory: 3.3M
        CPU: 39ms
     CGroup: /system.slice/nginx.service
             ├─1448 "nginx: master process /usr/sbin/nginx"
             └─1449 "nginx: worker process"

Nov 14 20:41:24 web.tp5.b1 systemd[1]: Starting The nginx HTTP and reverse proxy server...
Nov 14 20:41:25 web.tp5.b1 nginx[1446]: nginx: [warn] server name "/var/www/site_web_nul" has suspicious symbols in /et>
Nov 14 20:41:25 web.tp5.b1 nginx[1446]: nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
Nov 14 20:41:25 web.tp5.b1 nginx[1446]: nginx: configuration file /etc/nginx/nginx.conf test is successful
Nov 14 20:41:25 web.tp5.b1 nginx[1447]: nginx: [warn] server name "/var/www/site_web_nul" has suspicious symbols in /et>
Nov 14 20:41:25 web.tp5.b1 systemd[1]: Started The nginx HTTP and reverse proxy server.
```
        🌞 Ouvrir le port firewall

```
[tom@web conf.d]$ sudo firewall-cmd --add-port=80/tcp --permanent
[sudo] password for tom:
success
[tom@web conf.d]$ sudo firewall-cmd --reload
success
```     
🌞 Visitez le serveur web !
```
[tom@node1 ~]$ curl 10.5.1.12:80
<h1>MEOW</h1>
```

🌞 Visualiser le port en écoute
```
[tom@web ~]$ sudo ss -l -t -n
[sudo] password for tom:
State         Recv-Q        Send-Q               Local Address:Port               Peer Address:Port       Process
LISTEN        0             128                        0.0.0.0:22                      0.0.0.0:*
LISTEN        0             511                        0.0.0.0:80                      0.0.0.0:*
LISTEN        0             128                           [::]:22                         [::]:*
LISTEN        0             511                           [::]:80                         [::]:*
```