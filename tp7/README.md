# II. SSH

     🌞 Effectuez une connexion SSH en vérifiant le fingerprint
```
PS C:\Users\tomma> ssh tom@10.7.1.11
The authenticity of host '10.7.1.11 (10.7.1.11)' can't be established.
ED25519 key fingerprint is SHA256:ML/KzgLX/4hJ+UZlGbdFgUhKrvgemYq3o4wMLvD4w14.
This host key is known by the following other names/addresses:
    C:\Users\tomma/.ssh/known_hosts:7: 10.4.1.254
    C:\Users\tomma/.ssh/known_hosts:8: 10.4.1.253
    C:\Users\tomma/.ssh/known_hosts:9: 10.4.1.12
    C:\Users\tomma/.ssh/known_hosts:10: 10.4.1.3
    C:\Users\tomma/.ssh/known_hosts:11: 10.5.1.11
    C:\Users\tomma/.ssh/known_hosts:12: 10.5.1.254
    C:\Users\tomma/.ssh/known_hosts:13: 10.5.1.12
    C:\Users\tomma/.ssh/known_hosts:14: 10.6.1.254
    (8 additional names omitted)
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.7.1.11' (ED25519) to the list of known hosts.
tom@10.7.1.11's password:
Last login: Thu Nov 23 11:30:51 2023 from 10.7.1.1
```

    🌞 Consulter l'état actuel

    vérifiez que le serveur SSH tourne actuellement sur le port 22/tcp
```
# If you want to change the port on a SELinux system, you have to tell
# SELinux about this change.
# semanage port -a -t ssh_port_t -p tcp #PORTNUMBER
#
🌞#Port 22🌞
#AddressFamily any
#ListenAddress 0.0.0.0
#ListenAddress ::
```
vérifiez que le serveur SSH est disponible actuellement sur TOUTES les IPs de la machine
```
[tom@routeur ~]$ sudo ss -tnl | grep 22
LISTEN 0      128          0.0.0.0:22        0.0.0.0:*
LISTEN 0      128             [::]:22           [::]:*
```
🌞 Modifier la configuration du serveur SSH
```
Port 23000
#AddressFamily any
listenAddress 10.7.1.254
#ListenAddress ::
```
🌞 Prouvez que le changement a pris effet
```
[tom@routeur ~]$ sudo ss -tnl | grep 23000
LISTEN 0      128       10.7.1.254:23000      0.0.0.0:*
```
🌞 Effectuer une connexion SSH sur le nouveau port
```
PS C:\Users\tomma> ssh tom@router -p 23000
The authenticity of host '[router]:23000 ([10.7.1.254]:23000)' can't be established.
ED25519 key fingerprint is SHA256:ML/KzgLX/4hJ+UZlGbdFgUhKrvgemYq3o4wMLvD4w14.
This host key is known by the following other names/addresses:
    C:\Users\tomma/.ssh/known_hosts:7: 10.4.1.254
    C:\Users\tomma/.ssh/known_hosts:8: 10.4.1.253
    C:\Users\tomma/.ssh/known_hosts:9: 10.4.1.12
    C:\Users\tomma/.ssh/known_hosts:10: 10.4.1.3
    C:\Users\tomma/.ssh/known_hosts:11: 10.5.1.11
    C:\Users\tomma/.ssh/known_hosts:12: 10.5.1.254
    C:\Users\tomma/.ssh/known_hosts:13: 10.5.1.12
    C:\Users\tomma/.ssh/known_hosts:14: 10.6.1.254
    (9 additional names omitted)
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[router]:23000' (ED25519) to the list of known hosts.
tom@router's password:
Last login: Fri Nov 24 09:30:24 2023 from 10.7.1.1
```

# 3. Connexion par clé
    🌞 Générer une paire de clés

```
    Répertoire : C:\Users\tomma\.ssh


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        24/11/2023     09:34            739 id_rsa.pub 
```

    🌞 Déposer la clé publique sur une VM   
```
tomma@AsusTM MINGW32 ~
$ ssh-copy-id tom@10.7.1.11
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/c/Users/tomma/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
tom@10.7.1.11's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'tom@10.7.1.11'"
and check to make sure that only the key(s) you wanted were added.

tomma@AsusTM MINGW32 ~
$ ssh tom@10.7.1.11
Last login: Fri Nov 24 09:41:06 2023 from 10.7.1.1
```

# C. Changement de fingerprint
    🌞 Supprimer les clés sur la machine router.tp7.b1
```
[tom@routeur ~]$ sudo rm -r /etc/ssh/ssh_host_*
[tom@routeur ~]$ ls /etc/ssh/
moduli  ssh_config  ssh_config.d  sshd_config  sshd_config.d
```
    🌞 Regénérez les clés sur la machine router.tp7.b1
```
[tom@routeur ~]$ sudo ssh-keygen -A
ssh-keygen: generating new host keys: RSA DSA ECDSA ED25519
[tom@routeur ~]$ ls /etc/ssh/
moduli        sshd_config       ssh_host_dsa_key.pub    ssh_host_ed25519_key      ssh_host_rsa_key.pub
ssh_config    sshd_config.d     ssh_host_ecdsa_key      ssh_host_ed25519_key.pub
ssh_config.d  ssh_host_dsa_key  ssh_host_ecdsa_key.pub  ssh_host_rsa_key
```
🌞 Tentez une nouvelle connexion au serveur
```
PS C:\Users\tomma> ssh tom@10.7.1.254 -p 23000
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ED25519 key sent by the remote host is
SHA256:0l6XvvSCX5ldUUAS7xxDoVQPGBgExp1qwCA68xlHAhw.
Please contact your system administrator.
Add correct host key in C:\\Users\\tomma/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in C:\\Users\\tomma/.ssh/known_hosts:28
Host key for [10.7.1.254]:23000 has changed and you have requested strict checking.
Host key verification failed.

```
# III. Web sécurisé

🌞 Montrer sur quel port est disponible le serveur web

```
[tom@web ~]$ sudo ss -tnl
State   Recv-Q  Send-Q   Local Address:Port     Peer Address:Port  Process
LISTEN  0       128            0.0.0.0:22            0.0.0.0:*
LISTEN  0       511            0.0.0.0:80            0.0.0.0:*
LISTEN  0       128               [::]:22               [::]:*
LISTEN  0       511               [::]:80               [::]:*
```
🌞 Générer une clé et un certificat sur web.tp7.b1

```
[tom@web ~]$ openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout server.key -out server.crt
........+.........+...+...+.............+......+......+..+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*...+..+...+......+......+.......+............+........+...+...+.........+.+.....................+.....+.+........+.+.................................+........+.............+......+...+..+...............+....+..............+...+...+......+.+.................+.+..+....+...+.....+.+.....+.......+.....+.........+.........+......+.........+.+...+..+.+.....+...+.+.........+.........+........+....+.....+...+.+............+..+.......+..............+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
.+...+...+............+..+.+...+..............+.+.....+.......+..+...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.+.+.........+..+....+...............+..............+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.................+......+.........+......+...+...............+.....+....+........+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:FR
State or Province Name (full name) []:Nouvelle Aquitaine
Locality Name (eg, city) [Default City]:Bordeaux
Organization Name (eg, company) [Default Company Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server's hostname) []:
Email Address []:
[tom@web ~]$
[tom@web ~]$ sudo mv server.key /etc/pki/tls/private/web.tp7.b1.key
[tom@web ~]$  sudo mv server.crt /etc/pki/tls/certs/web.tp7.b1.crt
[tom@web ~]$ sudo chown nginx:nginx /etc/pki/tls/private/web.tp7.b1.key
[tom@web ~]$ sudo chown nginx:nginx /etc/pki/tls/certs/web.tp7.b1.crt
[tom@web ~]$ sudo chmod 0400 /etc/pki/tls/private/web.tp7.b1.key
[tom@web ~]$ sudo chmod 0444 /etc/pki/tls/certs/web.tp7.b1.crt
```

🌞 Modification de la conf de NGINX
```
[tom@web ~]$ sudo cat /etc/nginx/conf.d/site_web_nul.conf
[sudo] password for tom:
server {
    # on change la ligne listen
    listen 10.7.1.12:443 ssl;

    # et on ajoute deux nouvelles lignes
    ssl_certificate /etc/pki/tls/certs/web.tp7.b1.crt;
    ssl_certificate_key /etc/pki/tls/private/web.tp7.b1.key;

    server_name www.site_web_nul.b1;
    root /var/www/site_web_nul;
}
[tom@web ~]$
``` 
🌞 Conf firewall
```
[tom@web ~]$ sudo firewall-cmd --add-port=443/tcp --permanent
success
[tom@web ~]$ sudo firewall-cmd --reload
success
[tom@web ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3
  sources:
  services: cockpit dhcpv6-client ssh
 🌞 ports: 443/tcp🌞
  protocols:
  forward: yes
  masquerade: yes
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

🌞 Redémarrez NGINX
```
[tom@web ~]$ sudo systemctl restart nginx
```
🌞 Prouvez que NGINX écoute sur le port 443/tcp
```
[tom@web ~]$ sudo ss -tnl | grep 443
[sudo] password for tom:
LISTEN 0      511        10.7.1.12:443       0.0.0.0:*
```
🌞 Visitez le site web en https
```
[tom@john ~]$ curl -k https://10.7.1.12
<h1>MEOW</h1>
```