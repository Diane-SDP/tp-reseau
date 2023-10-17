# I. Exploration locale en solo
## 1. Affichage d'informations sur la pile TCP/IP locale
**🌞 Affichez les infos des cartes réseau de votre PC**
```powershell
ipconfig /all
```
**Réponse :** 
```powershell
PS C:\Users\Diane> ipconfig /all
    Carte réseau sans fil Wi-Fi :
        Adresse phyique (MAC) : D8-80-83-D0-03-05
        Adresse IP : 10.33.48.151

    Carte Ethernet Ethernet :
        Adresse physique (MAC) : 08-BF-B8-2D-AF-1D
```
*La carte Ethernet n'a pas d'adresse IP car connecté à aucun réseau*

**🌞 Affichez votre gateway**   

```powershell
ipconfig
```
**Réponse :**
```powershell
PS C:\Users\Diane> ipconfig
   Carte réseau sans fil Wi-Fi :
        Passerelle par défaut. . . . . . . . . : 10.33.51.254
```
**🌞 Déterminer la MAC de la passerelle** 
```powershell
arp -a 10.33.51.254
```
**Réponse :**
```powershell
    Interface : 10.33.48.151
        Adresse Internet      Adresse physique      Type
        10.33.51.254          7c-5a-1c-cb-fd-a4     dynamique
```
**🌞 Trouvez comment afficher les informations sur une carte IP (Windows 11)**  

Panneau de configuration > Réseau/Internet > Centre Réseau Partage > Modifier les paramètre de partage > WI-FI > détails

    Adresse physique: D8-80-83-D0-03-05
    Adresse IPv4: 10.33.48.151
    Passerelle par défaut IPv4: 10.33.51.254

## 2. Modifications des informations 
### A. Modification d'adresse IP (part 1)
**🌞 Utilisez l'interface graphique de votre OS pour changer d'adresse IP :**

Paramètres > Paramètres réseau avancés > Afficher les propriétés supplémentaires (de WIFI) > Modifier l'attribution d'adresse IP (passer en manuel) > Choisir son IP puis enregister

**🌞 Il est possible que vous perdiez l'accès internet.** Que ce soit le cas ou non, expliquez pourquoi c'est possible de perdre son accès internet en faisant cette opération.

**Réponse :** C'est possible de perdre l'accès car l'adresse IP est l'adresse de la machine sur le réseau local. Si l'adresse IP est changée, la machine ne sera plus reconnue sur le réseau local et donc ne pourra plus accéder à internet. Pour rester dans le même réseau, on peut modifier que le dernier octet pour rester dans le même réseau que la passerelle. Il se peut aussi que l'on ai choisi une adresse IP déjà utilisé dans le réseau.

# II. Exploration locale en duo
## 3. Modification d'adresse IP
**🌞 Modification de nos adresses IP en utilisant le GUI (windows)**  
Adresse IP choisies :   10.10.10.33 et 10.10.10.34
  
**🌞 Vérifier à l'aide d'une commande que votre IP a bien été changée**
```powershell
ipconfig /all
```
**Réponse :**
```powershell
PS C:\Users\Diane>ipconfig /all
Carte Ethernet Ethernet :
     Adresse IPv4. . . . . . . . . . . . . .: 10.10.10.33 (préféré)
```
**🌞 Vérifier que les deux machines se joignent**
```powershell
ping 10.10.10.34
```
**Réponse :**
```powershell
PS C:\Users\Diane> ping 10.10.10.34
Statistiques Ping pour 10.10.10.34:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 3ms, Maximum = 6ms, Moyenne = 4ms
```
**🌞 Déterminer l'adresse MAC de votre correspondant**
```powershell
arp -a 10.10.10.34
```
**Réponse :**
```powershell
Interface : 10.10.10.33 
    Adresse Internet      Adresse physique      Type
     10.10.10.34          50-eb-f6-30-83-17     dynamique
```
## 4. Petit chat privé 🐱

**🌞 sur le PC serveur**  
```powershell
.\nc.exe -l -p 8888
```
**Réponse :**  
```powershell
PS C:\Users\Diane\Documents\netcat-1.11> ./nc.exe -l -p 8888
```
*Il faut attendre que le client se connecte car aucune réponse*  
**🌞 sur le PC client**
```powershell
./nc64.exe 10.10.10.34 8888
```
*nc64.exe au lieu de nc.exe car windows a supprimé nc.exe youpi*

**Réponse :**
Chat ouvert aves l'autre pc 

**🌞 Visualiser la connexion en cours**
```powershell
netstat -a -n -b | Select-String 8888 -Context 0,1
```
**Réponse :**
a mettre  

**🌞 Pour aller un peu plus loin**
```powershell
.\nc.exe -l -p 8888 -s 10.10.10.34
```
**Réponse :**  
a mettre 
## 5. Firewall
**🌞 Activez et configurez votre firewall**  
- **Autoriser le ping**

On crée 2 règles personnalisées:
- Une pour le trafic entrant avec un ICMP de type 0
![Alt text](<image/ping1.PNG>) 
- Une pour le trafic entrant avec un ICMP de type 8
![Alt text](<image/ping2.PNG>) 

- **Autoriser le traffic sur le port qu'utilise nc**

On crée aussi 2 règles (entrant et sortant), de type port cette fois : 

![Alt text](<image/règle_port1.PNG>) 
![Alt text](<image/règle_port2.PNG>) 
## 6. Utilisation d'un des deux comme gateway
**🌞Tester l'accès internet depuis le PC client**   
```powershell
ping 1.1.1.1
```
**Réponse :**
```powershell
PS C:\Users\Diane> ping 1.1.1.1

Envoi d’une requête 'Ping'  1.1.1.1 avec 32 octets de données :
Réponse de 1.1.1.1 : octets=32 temps=91 ms TTL=55
Réponse de 1.1.1.1 : octets=32 temps=64 ms TTL=55
Réponse de 1.1.1.1 : octets=32 temps=31 ms TTL=55
Réponse de 1.1.1.1 : octets=32 temps=14 ms TTL=55

Statistiques Ping pour 1.1.1.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 14ms, Maximum = 91ms, Moyenne = 50ms
```
**🌞 Prouver que la connexion Internet passe bien par l'autre PC**   
```powershell
tracert 10.10.10.34
``` 
**Réponse :**
```powershell
PS C:\Users\Maxime> tracert 10.10.10.34

Détermination de l’itinéraire vers 10.10.10.34 avec un maximum de 30 sauts.

  1     *        *        *     Délai d’attente de la demande dépassé.
  2     *        *        *     Délai d’attente de la demande dépassé.
  3     *        *        *     Délai d’attente de la demande dépassé.
  4     *        *        *     Délai d’attente de la demande dépassé.
  5     *        *        *     Délai d’attente de la demande dépassé.
  6     *        *        *     Délai d’attente de la demande dépassé.
  7     *        *        *     Délai d’attente de la demande dépassé.
  8     *        *        *     Délai d’attente de la demande dépassé.
  9     *        *        *     Délai d’attente de la demande dépassé.
 10     *        *        *     Délai d’attente de la demande dépassé.
 11     *        *        *     Délai d’attente de la demande dépassé.
 12     *        *        *     Délai d’attente de la demande dépassé.
 13     *        *        *     Délai d’attente de la demande dépassé.
 14     *        *        *     Délai d’attente de la demande dépassé.
 15     *        *        *     Délai d’attente de la demande dépassé.
 16     *        *        *     Délai d’attente de la demande dépassé.
 17     *        *        *     Délai d’attente de la demande dépassé.
 18     *        *        *     Délai d’attente de la demande dépassé.
 19     *        *        *     Délai d’attente de la demande dépassé.
 20     *        *        *     Délai d’attente de la demande dépassé.
 21     *        *        *     Délai d’attente de la demande dépassé.
 22     *        *        *     Délai d’attente de la demande dépassé.
 23     *        *        *     Délai d’attente de la demande dépassé.
 24     *        *        *     Délai d’attente de la demande dépassé.
 25     *        *        *     Délai d’attente de la demande dépassé.
 26     *        *        *     Délai d’attente de la demande dépassé.
 27     *        *        *     Délai d’attente de la demande dépassé.
 28     *        *        *     Délai d’attente de la demande dépassé.
 29     *        *        *     Délai d’attente de la demande dépassé.
 30     *        *        *     Délai d’attente de la demande dépassé.

Itinéraire déterminé.
``` 
# III. Manipulations d'autres outils/protocoles côté client
## 1. DHCP d'autres outils/protocoles côté client
**🌞Exploration du DHCP, depuis votre PC**
```powershell
ipconfig /all
```
**Reponse :**
```powershell
PS C:\Users\Diane> ipconfig /all
Serveur DHCP . . . . . . . . . . . . . : 10.33.51.254
Bail expirant. . . . . . . . . . . . . : mardi 17 octobre 2023 13:43:39
```
## 2. DNS
🌞**Trouver l'adresse IP du serveur DNS que connaît votre ordinateur**
```powershell
ipconfig /all
```
**Réponse :**
```powershell
PS C:\Users\Diane> ipconfig /all
Carte réseau sans fil Wi-Fi :
Serveurs DNS. . .  . . . . . . . . . . : 10.33.50.2
                                         8.8.8.8
```
**🌞 Utiliser, en ligne de commande l'outil nslookup**
- **Un lookup pour google.com**
```powershell
nslookup google.com 8.8.8.8 
```
**Réponse :** 
```powershell
PS C:\Users\Diane> nslookup google.com 8.8.8.8
Serveur :   dns.google
Address:  8.8.8.8

Réponse ne faisant pas autorité :
Nom :    google.com
Addresses:  2a00:1450:4007:818::200e
          142.250.178.142
```
- **Puis pour Ynov :** 
```powershell
nslookup ynov.com 8.8.8.8
```
**Réponse :**
```
PS C:\Users\Diane> nslookup ynov.com 8.8.8.8
Serveur :   dns.google
Address:  8.8.8.8

Réponse ne faisant pas autorité :
Nom :    ynov.com
Addresses:  2606:4700:20::681a:be9
          2606:4700:20::681a:ae9
          2606:4700:20::ac43:4ae2
          104.26.11.233
          104.26.10.233
          172.67.74.226
``` 
L'adresse IP du serveur à qui on vient d'effectuer ces requêtes est 8.8.8.8
- **Un lookup pour l'adresse : 231.34.113.12**
```powershell
nslookup 231.34.113.12
```
**Reponse :** 
```
PS C:\Users\Diane> nslookup 231.34.113.12 8.8.8.8
Serveur :   dns.google
Address:  8.8.8.8

*** dns.google ne parvient pas à trouver 231.34.113.12 : Non-existent domain
```
Le DNS de google ne connait pas le nom du domaine associé à cette adresse IP
- **Un lookup pour l'adresse : 231.34.113.12**
```powershell
nslookup 78.34.2.17
```
**Reponse :**
```powershell
PS C:\Users\Diane> nslookup 78.34.2.17 8.8.8.8
Serveur :   dns.google
Address:  8.8.8.8

Nom :    cable-78-34-2-17.nc.de
Address:  78.34.2.17
```

Le DNS de google connait le nom du domaine associé à cette adresse IP qui est : cable-78-34-2-17.nc.de

# IV. Wireshark

**🌞 Utilisez le pour observer les trames qui circulent entre vos deux carte Ethernet. Mettez en évidence :**  
- **un ping entre vous et votre mate**
```powershell
ping 10.10.10.33
```
**Réponse :**
![Alt text](image/10.10.10.33.PNG)  
- **un ping entre vous et la passerelle du réseau**
```powershell
ping 10.10.10.34
```
**Réponse :**
![Alt text](image/ping.PNG)  
- **un netcat entre vous et votre mate, branché en RJ45**
```powershell
.\nc.exe -l -p 8888 
.\nc.exe 10.10.10.34 8888
```
**Réponse :**
![Alt text](image/netcat.PNG)
- **Identifiez dans la capture le serveur DNS à qui vous posez la question.**
```powershell
nslookup 8.8.8.8 10.10.10.34
```
**Réponse :**
![Alt text](image/dns-1.PNG)
*On pose la question a l'ordinateur qui possede internet*