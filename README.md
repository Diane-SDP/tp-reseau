# TP 1

## I. 1

🌞 **Infos cartes réseau**

`>> ipconfig /all`

> Résultat

    Carte réseau sans fil Wi-Fi :
        Adresse phyique (MAC) : D8-80-83-D0-03-05
        Adresse IP : 10.33.48.151

    Carte Ethernet Ethernet :
        Adresse physique (MAC) : 08-BF-B8-2D-AF-1D
*La carte Ethernet n'a pas d'adresse IP car connecté à aucun réseau*

🌞 **Adresse IP Passerelle**

`>> ipconfig /all`

> Résultat

    Carte réseau sans fil Wi-Fi :
        Passerelle par défaut. . . . . . . . . : 10.33.51.254

🌞 **Adresse MAC Passerelle**

`>> arp -a`

> Résultat

    Interface : 10.33.48.151
        Adresse Internet      Adresse physique      Type
        10.33.51.254          7c-5a-1c-cb-fd-a4     dynamique
*Adresse MAC de la passerelle :*  7c-5a-1c-cb-fd-a4

🌞 **En utilisant le GUI (windows)**

Panneau de configuration > Réseau/Internet > Centre Réseau Partage > Modifier les paramètre de partage > WI-FI > détails

> Résultat

    Adresse physique: D8-80-83-D0-03-05
    Adresse IPv4: 10.33.48.151
    Passerelle par défaut IPv4: 10.33.51.254

## I. 2

🌞 **Modification de l'adresse IP en utilisant le GUI (windows)**

Paramètres > Paramètres réseau avancés > Afficher les propriétés supplémentaires (de WIFI) > Modifier l'attribution d'adresse IP (passer en manuel) > Choisir son IP puis enregister

## II. 

🌞 **Modification de nos adresses IP en utilisant le GUI (windows)**

Adresse IP choisis : 10.10.10.33 et 10.10.10.32

`>> ipconfig /all`

> Résultat sur ma machine :

    Carte Ethernet Ethernet :
        Adresse IPv4. . . . . . . . . . . . . .: 10.10.10.33 (préféré)

`>> ping 10.10.10.32`

> Résultat sur ma machine :

    Statistiques Ping pour 10.10.10.32:
        Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
    Durée approximative des boucles en millisecondes :
        Minimum = 3ms, Maximum = 6ms, Moyenne = 4ms

`>> arp -a`

> Résultat sur ma machine :

    Interface : 10.10.10.33 
        Adresse Internet      Adresse physique      Type
        10.10.10.32           e4-a8-df-d1-98-ca     dynamique

*Adresse MAC de l'autre machine :*  e4-a8-df-d1-98-ca

🌞 **NETCAT (meow) -- PC Serveur**

`>> .\nc64.exe -l -p 8888`
*nc64 au lieu de nc car nc a été supprimé par Windows Defender youpi*

> Résultat sur ma machine :

    Chat avec le PC client