# TP5 : TCP, UDP et services réseau

## I. First steps

🌞 Déterminez, pour ces 5 applications, si c'est du TCP ou de l'UDP

- Discord  
Protocol : UDP  
Mon ip et mon port : 10.33.70.185:57400  
Ip du serveur et son port : 66.22.197.137:50001

- Ark  
Protocol : UDP  
Mon ip et mon port : 10.33.70.185:63875  
Ip du serveur et son port : 162.159.133.233:443

- Steam  
Protocol : TCP  
Mon ip et mon port : 10.33.70.185:55816  
Ip du serveur et son port : 185.25.182.4:443

- Whatsapp  
Protocol : UDP  
Mon ip et mon port : 10.33.70.185:52887  
Ip distante et son port : 92.88.3.32:3478

- Youtube
Protocol : UDP
Mon ip et mon port : 10.33.70.185:63608
Ip du serveur et son port : 77.136.192.79:443

🌞 Demandez l'avis à votre OS

```powershell
PS C:\Windows\system32> netstat

Connexions actives

  Proto  Adresse locale         Adresse distante       État

  # Discord
  UDP    10.33.70.185:57400     66.22.197.137:50001    ESTABLISHED
  # ARK
  UDP    10.33.70.185:63875     162.159.133.233:https  ESTABLISHED
  # Steam
  TCP    10.33.70.185:55816     185.25.182.4:https     ESTABLISHED
  # Whatsapp
  UDP    10.33.70.185:52887     92.88.3.32:3478        ESTABLISHED
  # Youtube
  UDP    10.33.70.185:63608     77.136.192.79:https    ESTABLISHED
```

## II. Setup virtuel

### 1. SSH

🌞 **Examinez le trafic dans Wireshark**

- déterminez si SSH utilise TCP ou UDP

*Réponse* : SSH utilise TCP

- repérez le 3-Way Handshake à l'établissement de la connexion
- repérez du trafic SSH
- repérez le FIN ACK à la fin d'une connexion

--> Voir [ce fichier pcapng](tp5_3_way.pcapng)

🌞 **Demandez aux OS**

- repérez, avec une commande adaptée (netstat ou ss), la connexion SSH depuis votre machine

```powershell
PS C:\Users\Diane> netstat

Connexions actives

  Proto  Adresse locale         Adresse distante       État
  TCP    10.5.1.1:57718         10.5.1.11:ssh          ESTABLISHED
```

- ET repérez la connexion SSH depuis la VM

```powershell
[diane@node1 ~]$ ss -t
State            Recv-Q            Send-Q                       Local Address:Port                         Peer Address:Port             Process
ESTAB            0                 0                                10.5.1.11:ssh                              10.5.1.1:58762
```

### 2. Routage

🌞 **Prouvez que**

- node1.tp5.b1 a un accès internet

```powershell
[diane@node1 network-scripts]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=112 time=19.5 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=112 time=19.0 ms
^C
--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
```

- node1.tp5.b1 peut résoudre des noms de domaine publics (comme ynov.com)

```powershell
[diane@node1 network-scripts]$ ping hentaihaven.com
PING hentaihaven.com (188.114.96.2) 56(84) bytes of data.
64 bytes from 188.114.96.2 (188.114.96.2): icmp_seq=1 ttl=55 time=21.2 ms
64 bytes from 188.114.96.2 (188.114.96.2): icmp_seq=2 ttl=55 time=22.3 ms
64 bytes from 188.114.96.2 (188.114.96.2): icmp_seq=3 ttl=55 time=22.9 ms
^C
--- hentaihaven.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
```

### Serveur WEB

🌞 **Installez le paquet nginx**

```powershell
[diane@servweb ~]$ sudo dnf install nginx -y
```

🌞 **Créer le site web**

```powershell
[diane@servweb var]$ sudo mkdir www
[diane@servweb www]$ sudo mkdir site_web_nul
[diane@servweb site_web_nul]$ sudo nano index.html

# Dans le fichier
<h1>MAAAAAaaaaaaAAAAAaaaAAAAx</h1>
<h2>UWU</h2>
```

🌞 **Donner les bonnes permissions**

```powershell
[diane@servweb ~]$ sudo chown -R nginx:nginx /var/www/site_web_nul
```

🌞 **Créer un fichier de configuration NGINX pour notre site web**

```powershell
[diane@servweb ~]$ sudo nano /etc/nginx/conf.d/site_web_nul.conf
```

🌞 **Démarrer le serveur web !**

```powershell
[diane@servweb conf.d]$ sudo systemctl start nginx
[diane@servweb conf.d]$ sudo systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; prese>
     Active: active (running) since Fri 2023-11-10 16:54:32 CET; 11s ago
```

🌞 **Ouvrir le port firewall**

```powershell
[diane@servweb ~]$ sudo firewall-cmd --add-port=80/tcp --permanent
success
[diane@servweb ~]$ sudo firewall-cmd --reload
success
```

🌞 **Visitez le serveur web !**

```powershell
[diane@node1 ~]$ curl http://10.5.1.12
<h1>MAAAAAaaaaaaAAAAAaaaAAAAx</h1>
<h2>UWU</h2>
```

🌞 **Visualiser le port en écoute**

```powershell
[diane@servweb ~]$ ss -atnl
State             Recv-Q            Send-Q                       Local Address:Port                         Peer Address:Port            Process
LISTEN            0                 511                                0.0.0.0:80                                0.0.0.0:*
```

🌞 **Analyse trafic**

```powershell
[diane@servweb ~]$ sudo tcpdump -i enp0s3 -w tp5_web.pcapng not port 22
tcpdump: listening on enp0s3, link-type EN10MB (Ethernet), snapshot length 262144 bytes
^C10 packets captured
10 packets received by filter
0 packets dropped by kernel
[diane@servweb ~]$ exit
logout
Connection to 10.5.1.12 closed.
PS C:\Users\Diane> scp diane@10.5.1.12:/home/diane/tp5_web.pcapng ./
diane@10.5.1.12's password:
tp5_web.pcapng                                                                      100% 1217     1.1MB/s   00:00
```

- repérez le contenu de la page HTML retourné (oui c'est visible direct dans Wireshark)

C'est dans la trame HTTP c joli woah

voir le joli fichier [meow](tp5_web.pcapng)