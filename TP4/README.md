# I. DHCP Client

🌞 Déterminer

- l'adresse du serveur DHCP
- l'heure exacte à laquelle vous avez obtenu votre bail DHCP
- l'heure exacte à laquelle il va expirer

```bash
PS C:\Users\Diane> ipconfig /all
Carte réseau sans fil Wi-Fi :

   Bail obtenu. . . . . . . . . . . . . . : vendredi 27 octobre 2023 08:42:20
   Bail expirant. . . . . . . . . . . . . : samedi 28 octobre 2023 08:42:12
   Serveur DHCP . . . . . . . . . . . . . : 10.33.79.254
```

🌞 Analyser la capture Wireshark (tcp_dhcp_client.pcapng)

Parmi ces 4 trames, laquelle contient les informations proposées au client ?

*Réponse :*  DHCP Offer

# II. Serveur DHCP

🌞 Preuve de mise en place

```bash
# Chez dhcp
[diane@dhcp ~]$ ping pornhub.com
PING pornhub.com (66.254.114.41) 56(84) bytes of data.
64 bytes from reflectededge.reflected.net (66.254.114.41): icmp_seq=1 ttl=53 time=15.4 ms
64 bytes from reflectededge.reflected.net (66.254.114.41): icmp_seq=2 ttl=53 time=14.7 ms
64 bytes from reflectededge.reflected.net (66.254.114.41): icmp_seq=3 ttl=53 time=25.1 ms
^C
--- pornhub.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
rtt min/avg/max/mdev = 14.719/18.407/25.135/4.764 ms
```

```bash
# Chez node2
[diane@node2 ~]$ ping jacquieetmichel.net
PING jacquieetmichel.net (104.19.140.72) 56(84) bytes of data.
64 bytes from 104.19.140.72 (104.19.140.72): icmp_seq=1 ttl=55 time=14.5 ms
64 bytes from 104.19.140.72 (104.19.140.72): icmp_seq=2 ttl=55 time=26.5 ms
64 bytes from 104.19.140.72 (104.19.140.72): icmp_seq=3 ttl=55 time=28.2 ms
^C
--- jacquieetmichel.net ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 14.465/23.051/28.193/6.110 ms
```

```bash
# Chez node2
[diane@node2 ~]$ traceroute 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  _gateway (10.4.1.254)  0.591 ms  0.597 ms  0.546 ms
 2  10.0.3.2 (10.0.3.2)  30.577 ms  30.570 ms  30.505 ms
```

🌞 Serveur DHCP

**Installation du logiciel**

```bash
[diane@dhcp ~]$ dnf -y install dhcp-server
```

**Configuration du dhcp**

```bash
[diane@dhcp ~]$ sudo nano /etc/dhcp/dhcpd.conf
[diane@dhcp ~]$ sudo cat /etc/dhcp/dhcpd.conf

# Nom de domaine
option domain-name     "tp4.dhcp";
# active le dhcp dans ce lan spécifique
authoritative;
# specify network address and subnetmask
subnet 10.4.1.0 netmask 255.255.255.0 {
    # IP doit etre entre 10.4.1.137 et 10.4.1.237
    range dynamic-bootp 10.4.1.137 10.4.1.237;
    # le broadcast
    option broadcast-address 10.4.1.255;
}
```

**Démarrage du serveur DHCP**

```bash
[diane@dhcp ~]$ sudo systemctl enable --now dhcpd
Created symlink /etc/systemd/system/multi-user.target.wants/dhcpd.service → /usr/lib/systemd/system/dhcpd.service.
[diane@dhcp ~]$ sudo firewall-cmd --add-service=dhcp
success
[diane@dhcp ~]$ sudo firewall-cmd --runtime-to-permanent
success
```

**Vérification de l'état du serveur DHCP**

```bash
[diane@dhcp ~]$ sudo systemctl status dhcpd
● dhcpd.service - DHCPv4 Server Daemon
     Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; enabled; preset>
     Active: active (running) since Fri 2023-10-27 15:03:30 CEST; 4min 49s >
```

### Client DHCP

🌞 Test !

```bash
[diane@dhcp ~]$ sudo cat /etc/sysconfig/network-scripts/ifcfg-enp0s3
DEVICE=enp0s3

BOOTPROTO=dhcp
ONBOOT=yes
```

🌞 Prouvez que

- node1.tp4.b1 a bien récupéré une IP dynamiquement

```bash
[diane@localhost ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:de:36:da brd ff:ff:ff:ff:ff:ff
    inet 10.4.1.137/24 brd 10.4.1.255 scope global ✨dynamic✨ noprefixroute enp0s3
       valid_lft 336sec preferred_lft 336sec
    inet6 fe80::a00:27ff:fede:36da/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```

```bash
[diane@localhost ~]$ nmcli con show enp0s3
```

- node1.tp4.b1 a enregistré un bail DHCP
   - **déterminer la date exacte de création du bail**

      vendredi 27 octobre 2023 15:19:21 GMT+02:00 DST
   - **déterminer la date exacte d'expiration**

      vendredi 27 octobre 2023 16:04:22 GMT+02:00 DST
   - **déterminer l'adresse IP du serveur DHCP**

      dhcp_server_identifier = 10.4.1.253

- Vous pouvez ping router.tp4.b1 et node2.tp4.b1 grâce à cette nouvelle IP récupérée

```bash
[diane@localhost ~]$ ping 10.4.1.254
PING 10.4.1.254 (10.4.1.254) 56(84) bytes of data.
64 bytes from 10.4.1.254: icmp_seq=1 ttl=64 time=0.285 ms
64 bytes from 10.4.1.254: icmp_seq=2 ttl=64 time=0.395 ms
^C
--- 10.4.1.254 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1036ms
rtt min/avg/max/mdev = 0.285/0.340/0.395/0.055 ms
[diane@localhost ~]$ ping 10.4.1.12
PING 10.4.1.12 (10.4.1.12) 56(84) bytes of data.
64 bytes from 10.4.1.12: icmp_seq=1 ttl=64 time=0.407 ms
64 bytes from 10.4.1.12: icmp_seq=2 ttl=64 time=0.349 ms
^C
--- 10.4.1.12 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1044ms
rtt min/avg/max/mdev = 0.349/0.378/0.407/0.029 ms
```

🌞 Bail DHCP serveur


### Options DHCP

🌞 Nouvelle conf !

```bash
[diane@dhcp ~]$ sudo nano /etc/dhcp/dhcpd.conf
[diane@dhcp ~]$ sudo cat /etc/dhcp/dhcpd.conf
# Nom de domaine
option domain-name     "tp4.dhcp";
# DNS
option domain-name-servers     1.1.1.1;
# temps d'expiration par défaut 
default-lease-time 21600;
# temsp d'expiration max (si client veut choisir un temps d'expiration précis)
max-lease-time 21600;
# active le dhcp dans ce lan spécifique
authoritative;
# adresse réseau et masque de sous réseau
subnet 10.4.1.0 netmask 255.255.255.0 {
    range dynamic-bootp 10.4.1.137 10.4.1.237;
    # broadcast
    option broadcast-address 10.4.1.255;
    # passerelle
    option routers 10.4.1.254;
}
[diane@dhcp ~]$ sudo systemctl restart dhcpd

```

🌞 Test !

- vous avez enregistré l'adresse d'un serveur DNS

```bash
[diane@localhost ~]$ sudo cat /etc/resolv.conf
# Generated by NetworkManager
search tp4.dhcp
nameserver 1.1.1.1
```

- vous avez une nouvelle route par défaut qui a été récupérée dynamiquement

```bash
[diane@localhost ~]$ ip r s
default via 10.4.1.254 dev enp0s3 proto dhcp src 10.4.1.138 metric 100
```

- la durée de votre bail DHCP est bien de 6 heures

```bash
[diane@localhost ~]$ nmcli con show enp0s3
```

- prouvez que vous avez un accès Internet après cet échange DHCP

```bash
[diane@localhost ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=110 time=29.3 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=110 time=37.0 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=110 time=31.2 ms
^C
--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
rtt min/avg/max/mdev = 29.268/32.503/37.043/3.305 ms
[diane@localhost ~]$ ping youtube.com
PING youtube.com (172.217.18.206) 56(84) bytes of data.
64 bytes from ham02s14-in-f206.1e100.net (172.217.18.206): icmp_seq=1 ttl=115 time=30.7 ms
64 bytes from par10s38-in-f14.1e100.net (172.217.18.206): icmp_seq=2 ttl=115 time=28.9 ms
64 bytes from par10s38-in-f14.1e100.net (172.217.18.206): icmp_seq=3 ttl=115 time=27.8 ms
^C
--- youtube.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
rtt min/avg/max/mdev = 27.813/29.147/30.742/1.209 ms
```

🌞 Capture Wireshark
