# TP1 

# Affichez les infos des cartes réseau de votre PC

nom, adresse MAC et adresse IP de l'interface WiFi
```
    Adresse MAC : 30-89-4A-D2-5A-AA
    Adresse IP : 10.33.48.10
```
nom, adresse MAC et adresse IP de l'interface Ethernet
    Carte Ethernet Ethernet :
```
   Adresse physique . . . . . . . . . . . : 08-BF-B8-C2-2A-57
   Adresse IP  . . . . . . . . . . . . . . . . .: 192.168.27.1
```
# Affichez votre gateway
    Carte Ethernet VMware Network Adapter VMnet1 : Pas de passerelle spécifiée.
    Carte Ethernet VMware Network Adapter VMnet8 : Pas de passerelle spécifiée.
    Carte réseau sans fil Wi-Fi : Passerelle par défaut : 10.33.51.254

 # Déterminer la MAC de la passerelle   

    Adresse IP : 192.168.192.254
    Adresse MAC : 00-50-56-f1-ee-29
    Type : Dynamique

# Trouvez comment afficher les informations sur une carte IP (change selon l'OS)
    Adresse IP de l'interface WiFi : 10.33.48.10 (préféré)
    Adresse MAC (adresse physique) de l'interface WiFi : 30-89-4A-D2-5A-AA
    Passerelle (gateway) de l'interface WiFi : 10.33.51.254

# 2
    mon ip modifiée 10.10.10.12
    🌞 Vérifier à l'aide d'une commande que votre IP a bien été changée
```
carte Ethernet Ethernet :
   Suffixe DNS propre à la connexion. . . :
   Description. . . . . . . . . . . . . . : Realtek PCIe GbE Family Controller
   Adresse physique . . . . . . . . . . . : 08-BF-B8-C2-2A-57
   DHCP activé. . . . . . . . . . . . . . : Non
   Configuration automatique activée. . . : Oui
   Adresse IPv6 de liaison locale. . . . .: fe80::ba35:d153:bebc:3f93%20(préféré)
   Adresse IPv4. . . . . . . . . . . . . .: 10.10.10.12(préféré)
   Masque de sous-réseau. . . . . . . . . : 255.255.255.0
   Passerelle par défaut. . . . . . . . . :
   IAID DHCPv6 . . . . . . . . . . . : 201899960
   DUID de client DHCPv6. . . . . . . . : 00-01-00-01-2C-0E-AB-60-08-BF-B8-C2-2A-57
   NetBIOS sur Tcpip. . . . . . . . . . . : Activé
```
🌞 Vérifier que les deux machines se joignent
    ping 10.10.10.11
```
Envoi d’une requête 'Ping'  10.10.10.11 avec 32 octets de données :
Réponse de 10.10.10.11 : octets=32 temps=2 ms TTL=128
Réponse de 10.10.10.11 : octets=32 temps=2 ms TTL=128
Réponse de 10.10.10.11 : octets=32 temps=2 ms TTL=128
Réponse de 10.10.10.11 : octets=32 temps=2 ms TTL=128

Statistiques Ping pour 10.10.10.11:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 2ms, Maximum = 2ms, Moyenne = 2ms
```
    🌞 sur le PC serveur 
Cmd line: -l -p 8888
    🌞 sur le PC client
10.10.10.12 8888

🌞 Visualiser la connexion en cours
```
     [nc64.exe]
  TCP    10.33.48.10:139        0.0.0.0:0              LISTENING
 Impossible d’obtenir les informations de propriétaire
  TCP    10.33.48.10:7680       10.33.50.159:49645     TIME_WAIT
  TCP    10.33.48.10:7680       10.33.50.210:51211     TIME_WAIT
  TCP    10.33.48.10:7680       10.33.51.29:61070      TIME_WAIT
  TCP    10.33.48.10:7680       10.33.51.29:61087      TIME_WAIT
  TCP    10.33.48.10:49411      20.199.120.151:443     ESTABLISHED
  WpnService
  ```
🌞 Pour aller un peu plus loin
 ``` 
PS D:\netcat-1.11>  .\nc64.exe -l -p 8888
yttt
ujjjj
PS D:\netcat-1.11>  .\nc64.exe -l -p 8888
PS D:\netcat-1.11>  .\nc64.exe -l -p 8888 -s 10.10.10.12
jhgf
```
🌞 Activez et configurez votre firewall


🌞Tester l'accès internet

```

```

🌞 Prouver que la connexion Internet passe bien par l'autre PC