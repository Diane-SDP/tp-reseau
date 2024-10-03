# TP1 : Maîtrise réseau du votre poste

## Basics

☀️ Carte réseau WiFi

```powershell
ipconfig
```

Carte réseau sans fil Wi-Fi :
   Adresse MAC . . . . . . . . . . . : D8-80-83-D0-03-05
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.79.236
   Masque de sous-réseau (décimale). . . . . . . . . : 255.255.240.0
   Masque de sous-réseau (CIDR). . . . . . . . . : 10.33.79.236/20

☀️ Déso pas déso

4094 IP disponible dans le réseau
Network adress : 10.33.64.0
Broadcast adress : 10.33.79.255

☀️ Hostname

```powershell
PS C:\Users\Diane> $env:computername
ASUS-DIANE
```

☀️ Passerelle du réseau

Adresse IP. . . . . . . . . : 10.33.79.254
Adresse MAC. . . . . . . . . :7c-5a-1c-d3-d8-76

☀️ Serveur DHCP et DNS

DHCP IP : 10.33.79.254
DNS IP : 8.8.8.8

☀️ Table de routage

Destination réseau    Masque réseau  Adr. passerelle   Adr. interface 
          0.0.0.0     0.0.0.0        10.33.79.254      10.33.79.236     

## II. Go further

☀️ Hosts ?

```powershell
PS C:\Windows\System32\drivers\etc> type .\hosts
# Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
#
# This file contains the mappings of IP addresses to host names. Each
# entry should be kept on an individual line. The IP address should
# be placed in the first column followed by the corresponding host name.
# The IP address and the host name should be separated by at least one
# space.
#
# Additionally, comments (such as these) may be inserted on individual
# lines or following the machine name denoted by a '#' symbol.
#
# For example:
#
#      102.54.94.97     rhino.acme.com          # source server
#       38.25.63.10     x.acme.com              # x client host

# localhost name resolution is handled within DNS itself.
#       127.0.0.1       localhost
#       ::1             localhost

1.1.1.1 b2.hello.vous
PS C:\Users\Diane> ping b2.hello.vous

Envoi d’une requête 'ping' sur b2.hello.vous [1.1.1.1] avec 32 octets de données :
Réponse de 1.1.1.1 : octets=32 temps=39 ms TTL=55
Réponse de 1.1.1.1 : octets=32 temps=19 ms TTL=55
Réponse de 1.1.1.1 : octets=32 temps=22 ms TTL=55
Réponse de 1.1.1.1 : octets=32 temps=27 ms TTL=55

Statistiques Ping pour 1.1.1.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 19ms, Maximum = 39ms, Moyenne = 26ms
```

☀️ Go mater une vidéo youtube et déterminer, pendant qu'elle tourne...

Serveur source : 77.136.192.81
Port source :  443
Port destination : 62146

☀️ Requêtes DNS

```powershell
PS C:\Users\Diane> nslookup  www.thinkerview.com
Serveur :   dns.google
Address:  8.8.8.8

Réponse ne faisant pas autorité :
Nom :    www.thinkerview.com
Addresses:  2a06:98c1:3120::7
          2a06:98c1:3121::7
          **188.114.97.7**
          188.114.96.7

PS C:\Users\Diane> nslookup  143.90.88.12
Serveur :   dns.google
Address:  8.8.8.8

Nom :    EAOcf-140p12.ppp15.odn.ne.jp
Address:  143.90.88.12
```

☀️ Hop hop hop

```powershell
PS C:\Users\Diane> tracert -d www.ynov.com

Détermination de l’itinéraire vers www.ynov.com [104.26.10.233]
avec un maximum de 30 sauts :

  1     4 ms     5 ms     6 ms  10.33.79.254
  2     5 ms     4 ms     5 ms  195.7.117.145
  3     6 ms     5 ms     4 ms  86.79.195.237
  4     6 ms     5 ms     6 ms  86.65.224.196
  5    16 ms    13 ms    14 ms  194.6.147.164
  6     *        *       48 ms  162.158.20.24
  7    18 ms    19 ms    22 ms  162.158.20.240
  8    20 ms    18 ms    19 ms  104.26.10.233
```

☀️ IP publique

IP Publique : 195.7.117.146

## Le requin