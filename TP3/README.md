# <div align='center'>TP3 : On va router des trucs

## ARP

### 1. Echange ARP

Chez marcel

```bash
[diane@localhost ~]$ ping 10.3.1.11
PING 10.3.1.11 (10.3.1.11) 56(84) bytes of data.
64 bytes from 10.3.1.11: icmp_seq=1 ttl=64 time=0.392 ms
64 bytes from 10.3.1.11: icmp_seq=2 ttl=64 time=0.375 ms
64 bytes from 10.3.1.11: icmp_seq=3 ttl=64 time=0.288 ms
64 bytes from 10.3.1.11: icmp_seq=4 ttl=64 time=0.418 ms
^C
--- 10.3.1.11 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3062ms
rtt min/avg/max/mdev = 0.288/0.368/0.418/0.048 ms
```

Chez john

```bash
[diane@localhost ~]$ ping 10.3.1.12
PING 10.3.1.12 (10.3.1.12) 56(84) bytes of data.
64 bytes from 10.3.1.12: icmp_seq=1 ttl=64 time=0.289 ms
64 bytes from 10.3.1.12: icmp_seq=2 ttl=64 time=0.385 ms
64 bytes from 10.3.1.12: icmp_seq=3 ttl=64 time=0.424 ms
64 bytes from 10.3.1.12: icmp_seq=4 ttl=64 time=0.387 ms
^C
--- 10.3.1.12 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3110ms
rtt min/avg/max/mdev = 0.289/0.371/0.424/0.049 ms
```

-------------------------------------------

Chez john

```bash
[diane@localhost ~]$ ip n s
10.3.1.12 dev enp0s3 lladdr 08:00:27:9e:58:94 STALE
```

Chez marcel

```bash
[diane@localhost ~]$ ip n s
10.3.1.11 dev enp0s3 lladdr 08:00:27:42:4a:6f STALE
```

- une commande pour voir la MAC de marcel dans la table ARP de john

```bash
[diane@localhost ~]$ ip n s 10.3.1.12
10.3.1.12 dev enp0s3 lladdr ⭐08:00:27:9e:58:94⭐ STALE
```

- une commande pour afficher la MAC de marcel, depuis marcel

```bash
[diane@localhost ~]$ ip a
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether ⭐08:00:27:9e:58:94⭐ brd ff:ff:ff:ff:ff:ff
    inet 10.3.1.12/24 brd 10.3.1.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe9e:5894/64 scope link
       valid_lft forever preferred_lft forever
```

### 2. Analyse de trames

🌞Analyse de trames

Vider la table ARP chez john et marcel

```bash
sudo ip neigh flush all
```

Chez john, capture de trame

```bash
[diane@localhost ~]$ sudo tcpdump -i enp0s3 -c 20 -w tp3_arp.pcapng not port 22
```

Chez marcel, ping john

```bash
[diane@localhost ~]$ ping 10.3.1.11
```

- [Fichier pcapng avec une trame ARP resquest et une trame ARP reply](tp3_arp.pcapng)

## II. Routage

- Commande pour ajouter les routes statiques :
    - Pour marcel :
    
```bash
sudo ip route add 10.3.1.0/24 via 10.3.2.254 dev enp0s3
```

```bash
[diane@localhost ~]$ sudo nano /etc/sysconfig/network-scripts/route-enp0s3

# Dans le fichier
10.3.1.0/24 via 10.3.2.254 dev enp0s3 

[diane@localhost ~]$ sudo systemctl restart NetworkManager
```

- Pour john :

```bash
sudo ip route add 10.3.2.0/24 via 10.3.1.254 dev enp0s3
```

```bash
[slayz@localhost ~]$ sudo nano /etc/sysconfig/network-scripts/route-enp0s3

10.3.2.0/24 via 10.3.1.254 dev enp0s3

sudo systemctl restart NetworkManager

```

### Analyse de trames


sudo ip neigh flush all

sur routeur et marcel : sudo tcpdump not port 22

sur john : ping 10.3.2.12

🌞Analyse des échanges ARP

| ordre | type trame  | IP source           | MAC source                   | IP destination      | MAC destination              |
| ----- | ----------- | ------------------- | ---------------------------- | ------------------- | ---------------------------- |
| 1     | Requête ARP | x                   | `john` `08:00:27:42:4a:6f`   | x                   | Broadcast `FF:FF:FF:FF:FF`   |
| 2     | Réponse ARP | x                   | `routeur` `08:00:27:e7:0e:1a`| x                   | `john` `08:00:27:42:4a:6f`   |
| 3     | Ping        | `john` `10.3.1.11`  | `john` `08:00:27:42:4a:6f`   | `marcel` `10.3.2.12`| `routeur` `08:00:27:e7:0e:1a`|
| 3     | Ping        | `john` `10.3.1.11`  | `routeur``08:00:27:fb:fe:b0` | `marcel` `10.3.2.12`| `marcel` `08:00:27:9e:58:94` |
| 4     | Requête ARP | x                   | `marcel` `08:00:27:9e:58:94` | x                   | Broadcast `FF:FF:FF:FF:FF`   |
| 5     | Réponse ARP | x                   | `routeur` `08:00:27:fb:fe:b0`| x                   | `marcel` `08:00:27:9e:58:94` |
| 6     | Pong        | `marcel` `10.3.2.12`| `marcel` `08:00:27:9e:58:94` | `john` `10.3.1.11`  | `routeur` `08:00:27:fb:fe:b0`|
| 6     | Pong        | `marcel` `10.3.2.12`| `routeur` `08:00:27:e7:0e:1a`| `john` `10.3.1.11`  | `john` `08:00:27:42:4a:6f`   |

### Accès internet

🌞Donnez un accès internet à vos machines - config routeur

Vérification de l'accès à Internet (depuis routeur) :

```bash
[diane@localhost ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=21.6 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=19.1 ms
^C
--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1003ms
```

Autoriser le routage des paquets sur internet :

Ajouter une route par defaut :

```bash
[diane@localhost ~]$ sudo firewall-cmd --add-masquerade --permanent
success
[diane@localhost ~]$ sudo firewall-cmd --reload
success
```

🌞Donnez un accès internet à vos machines - config clients

```bash
# Ajouter route par défaut

[diane@localhost ~]$ sudo nano /etc/sysconfig/network
# Ajouter GATEWAY=10.3.1.254 (chez john) de GATEWAY=10.3.2.254 (chez marcel)
[diane@localhost ~]$ sudo systemctl restart NetworkManager

# Ajouter l'adresse d'un serveur DNS qu'ils peuvent utiliser
[diane@localhost ~]$ sudo nano /etc/resolv.conf
# Ajouter nameserver 1.1.1.1 (chez john ET marcel)
```

Vérification de l'accès à Internet (depuis marcel et john) :

```bash
[diane@localhost ~]$ ping google.com
PING google.com (142.250.75.238) 56(84) bytes of data.
64 bytes from par10s41-in-f14.1e100.net (142.250.75.238): icmp_seq=1 ttl=116 time=30.5 ms
64 bytes from par10s41-in-f14.1e100.net (142.250.75.238): icmp_seq=2 ttl=116 time=27.4 ms
64 bytes from par10s41-in-f14.1e100.net (142.250.75.238): icmp_seq=3 ttl=116 time=28.1 ms
^C
--- google.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
```

| ordre | type trame  | IP source           | MAC source                   | IP destination      | MAC destination              |
| ----- | ----------- | ------------------- | ---------------------------- | ------------------- | ---------------------------- |
| 1     | Ping        | `john` `10.3.1.11`  | `john` `08:00:27:42:4a:6f`   | `marcel` `10.3.2.12`| `routeur` `08:00:27:e7:0e:1a`|
| 2     | Ping        | `john` `10.3.1.11`  | `routeur` `08:00:27:fb:fe:b0`| `marcel` `10.3.2.12`| `marcel` `08:00:27:9e:58:94` |
| 3     | Pong        | `marcel` `10.3.2.12`| `marcel` `08:00:27:9e:58:94` | `john` `10.3.1.11`  | `routeur` `08:00:27:fb:fe:b0`|
| 4     | Pong        | `marcel` `10.3.2.12`| `routeur` `08:00:27:e7:0e:1a`| `john` `10.3.1.11`  | `john` `08:00:27:42:4a:6f`   |
