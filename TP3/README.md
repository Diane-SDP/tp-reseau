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

🌞Analyse des échanges ARP

| ordre | type trame  | IP source | MAC source                   | IP destination | MAC destination              |
| ----- | ----------- | --------- | ---------------------------- | -------------- | ---------------------------- |
| 1     | Requête ARP | x         | `john` `08:00:27:42:4a:6f`   | x              | Broadcast `FF:FF:FF:FF:FF`   |
| 2     | Réponse ARP | x         | `routeur` `08:00:27:e7:0e:1a`| x              | `john` `08:00:27:42:4a:6f`   |
| ...   | ...         | ...       | ...                          |                |                              |
| ?     | Ping        | ?         | ?                            | ?              | ?                            |
| ?     | Pong        | ?         | ?                            | ?              | ?                            |
