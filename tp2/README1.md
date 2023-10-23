# TP2 : Ethernet, IP, et ARP
    🌞 Mettez en place une configuration réseau fonctionnelle entre les deux machines
```
    Carte Ethernet Ethernet :

   Suffixe DNS propre à la connexion. . . :
   Adresse IPv6 de liaison locale. . . . .: fe80::ba35:d153:bebc:3f93%21
   Adresse IPv4. . . . . . . . . . . . . .: 10.10.10.1
   Masque de sous-réseau. . . . . . . . . : 255.255.255.252
   Passerelle par défaut. . . . . . . . . : 10.10.10.0
```
    🌞 Prouvez que la connexion est fonctionnelle entre les deux machines
```
PS C:\Users\tomma> ping 10.10.10.2

Envoi d’une requête 'Ping'  10.10.10.2 avec 32 octets de données :
Réponse de 10.10.10.2 : octets=32 temps=1 ms TTL=128
Réponse de 10.10.10.2 : octets=32 temps=1 ms TTL=128
Réponse de 10.10.10.2 : octets=32 temps=1 ms TTL=128
Réponse de 10.10.10.2 : octets=32 temps=1 ms TTL=128

Statistiques Ping pour 10.10.10.2:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 1ms, Maximum = 1ms, Moyenne = 1ms
```
    🌞 Wireshark it

```
voir ping-reseau-pv
```

# II. ARP my bro
    🌞 Check the ARP table

```
Interface : 10.10.10.1 --- 0x15
  Adresse Internet      Adresse physique      Type
  10.10.10.2            2c-f0-5d-6c-11-b8     dynamique
  10.10.10.3            ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
```
🌞 Manipuler la table ARP
```
PS C:\Users\tomma>  arp -d
La suppression de l'entrée ARP a échoué : L'opération demandée nécessite une élévation.
--------

Interface : 10.10.10.1 --- 0x15
  Adresse Internet      Adresse physique      Type
  10.10.10.2            2c-f0-5d-6c-11-b8     dynamique
  10.10.10.3            ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
  -----------
PS C:\Windows\system32> ping 8.8.8.8

Envoi d’une requête 'Ping'  8.8.8.8 avec 32 octets de données :
Réponse de 8.8.8.8 : octets=32 temps=21 ms TTL=112
Réponse de 8.8.8.8 : octets=32 temps=22 ms TTL=112
Réponse de 8.8.8.8 : octets=32 temps=22 ms TTL=112
Réponse de 8.8.8.8 : octets=32 temps=22 ms TTL=112

Statistiques Ping pour 8.8.8.8:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 21ms, Maximum = 22ms, Moyenne = 21ms
```
🌞 Wireshark it

```

```

# III. DHCP

🌞 Wireshark it