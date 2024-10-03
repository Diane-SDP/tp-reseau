# <div align='center'>TP2 : Ethernet, IP, et ARP 

- [Setup IP](#i-setup-ip)
- [ARP my bro](#ii-arp-my-bro)
- [DHCP](#iii-dhcp)

## I. Setup IP

**🌞 Mettez en place une configuration réseau fonctionnelle entre les deux machines**  

- IP choisi : 10.10.10.33/30 et 10.10.10.34/30  
- Adresse de réseau : 10.10.10.32  
- Adresse de broadcast : 10.10.10.35  

*commande utilisée pour définir les adresses IP via le powershell :*

```powershell
New-NetIPAddress –InterfaceIndex 14 –IPAddress 10.10.10.34   –PrefixLength 30
```

**Réponse :**  

```powershell
PS C:\WINDOWS\system32> New-NetIPAddress -InterfaceIndex 14 -IPAddress 10.10.10.34 -PrefixLength 30


IPAddress         : 10.10.10.34
InterfaceIndex    : 14
InterfaceAlias    : Ethernet
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 30
PrefixOrigin      : Manual
SuffixOrigin      : Manual
AddressState      : Tentative
ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
SkipAsSource      : False
PolicyStore       : ActiveStore

IPAddress         : 10.10.10.34
InterfaceIndex    : 14
InterfaceAlias    : Ethernet
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 30
PrefixOrigin      : Manual
SuffixOrigin      : Manual
AddressState      : Invalid
ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
SkipAsSource      : False
PolicyStore       : PersistentStore
```

*commande utilisée pour trouver l'index de l'ethernet :*

```powershell
netsh int ipv4 show interfaces
```

**Réponse :**  

```powershell
Idx     Mét         MTU          État                Nom
---  ----------  ----------  ------------  ---------------------------
  1          75  4294967295  connected     Loopback Pseudo-Interface 1
 12          55        1500  connected     Wi-Fi
 14          75        1500  connected     Ethernet
  4          25        1500  disconnected  Connexion au réseau local* 9
 18          25        1500  disconnected  Connexion au réseau local* 10
  3          35        1500  connected     VMware Network Adapter VMnet1
 19          35        1500  connected     VMware Network Adapter VMnet8
```

**🌞 Prouvez que la connexion est fonctionnelle entre les deux machines**

```powershell
ping 10.10.10.34
```

**Réponse :**

```powershell
PS C:\Users\Diane> ping 10.10.10.33

Envoi d’une requête 'Ping'  10.10.10.33 avec 32 octets de données :
Réponse de 10.10.10.33 : octets=32 temps=3 ms TTL=128
Réponse de 10.10.10.33 : octets=32 temps=4 ms TTL=128
Réponse de 10.10.10.33 : octets=32 temps=3 ms TTL=128
Réponse de 10.10.10.33 : octets=32 temps=4 ms TTL=128

Statistiques Ping pour 10.10.10.33:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 3ms, Maximum = 4ms, Moyenne = 3ms
```

**🌞 Wireshark it**  
On envoie un paquet ICMP de type 8 (demande d'echo) puis on reçoit un paquet ICMP de type 0 (réponse d'echo)

[Clique ici pour voir les paquets ICMP](./tp2.pcapng)

## II. ARP my bro

**🌞 Check the ARP table**

```powershell
arp -a 
```

**Réponse :**  

```powershell
PS C:\Users\Diane> arp -a 
Interface : 10.10.10.34 --- 0x16
  Adresse Internet      Adresse physique      Type
  10.10.10.33         ⭐50-eb-f6-30-83-17⭐  dynamique
  10.10.10.35           ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
```

- **déterminez la MAC de la gateway de votre réseau**

```powershell
arp -a 10.33.51.254
```

**Réponse :**

```powershell
PS C:\Users\Diane> arp -a 10.33.51.254
Interface : 10.33.51.254 --- 0x16
  Adresse Internet       Adresse physique     Type  
  10.33.51.254           7c-5a-1c-cb-fd-a4    dynamique
```

**🌞 Manipuler la table ARP**

- **Clear ARP**  

```powershell
netsh interface IP delete arpcache
```

**Réponse :**

```powershell
PS C:\Users\melb3> arp -a
Interface : 10.10.10.34 --- 0x16
  Adresse Internet      Adresse physique      Type
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
```

**Après un autre ping :**

**Réponse :**

```powershell
PS C:\Users\Diane> arp -a
Interface : 10.10.10.34 --- 0x16
  Adresse Internet      Adresse physique      Type
  10.10.10.33           50-eb-f6-30-83-17     dynamique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
```

[Clique ici pour voir les trames ARP](./arp.pcapng)  

1ère trame

```
Source: ASUSTekC_30:83:17 (50:eb:f6:30:83:17)
Destination: ASUSTekC_2d:af:1d (08:bf:b8:2d:af:1d)
```

2ème trame

```
Source: ASUSTekC_2d:af:1d   (08:bf:b8:2d:af:1d) 
Destination: ASUSTekC_30:83:17   (50:eb:f6:30:83:17)  
```

Chacune de ces adresses correspondent à l'adresse mac des pc reliés en ethernet

## III. DHCP

🌞 Wireshark it

[Clique ici pour voir l'echange DORA](./dhcp.pcapng)   

- Identifiez les 4 trames DHCP lors d'un échange DHCP
  - mettez en évidence les adresses source et destination de chaque trame
- Identifiez dans ces 4 trames les informations 1, 2 et 3 dont on a parlé juste au dessus

1 : 192.168.1.17  (Une IP à utiliser)  
2 : 192.168.1.254 (l'adresse IP de la passerelle du réseau)  
3 : 192.168.1.254 (l'adresse d'un serveur DNS joignable depuis ce réseau)

1ère trame

    Source: CloudNet_d0:03:05 (d8:80:83:d0:03:05)
    Destination: Broadcast (ff:ff:ff:ff:ff:ff)

2ème trame 

    Source: FreeboxS_0e:c5:55 (38:07:16:0e:c5:55)
    Destination: CloudNet_d0:03:05 (d8:80:83:d0:03:05)

3ème trame  

    Source: CloudNet_d0:03:05 (d8:80:83:d0:03:05)
    Destination: Broadcast (ff:ff:ff:ff:ff:ff)

4ème trame 

    Source: FreeboxS_0e:c5:55 (38:07:16:0e:c5:55)
    Destination: CloudNet_d0:03:05 (d8:80:83:d0:03:05)
 