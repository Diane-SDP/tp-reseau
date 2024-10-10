# TP2 : Environnement virtuel

## I. Topologie réseau

☀️ Sur node1.lan1.tp2

afficher ses cartes réseau

```powershell
[diane@node1.lan1.tp2 ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:ca:f7:a9 brd ff:ff:ff:ff:ff:ff
    inet 10.1.1.11/24 brd 10.1.1.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:feca:f7a9/64 scope link
       valid_lft forever preferred_lft forever
```

afficher sa table de routage

```powershell
[diane@node1.lan1.tp2 ~]$ ip r
10.1.1.0/24 dev enp0s3 proto kernel scope link src 10.1.1.11 metric 100
10.1.2.0/24 via 10.1.1.254 dev enp0s3 proto static metric 100
```

prouvez qu'il peut joindre node2.lan2.tp2

```powershell
[diane@node1.lan1.tp2 ~]$ ping 10.1.2.12
PING 10.1.2.12 (10.1.2.12) 56(84) bytes of data.
64 bytes from 10.1.2.12: icmp_seq=1 ttl=63 time=0.631 ms
64 bytes from 10.1.2.12: icmp_seq=2 ttl=63 time=0.601 ms
^C
--- 10.1.2.12 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1020ms
rtt min/avg/max/mdev = 0.601/0.616/0.631/0.015 ms
```

prouvez avec un traceroute que le paquet passe bien par router.tp2

```powershell
[diane@node1.lan1.tp2 ~]$ traceroute 10.1.2.11
traceroute to 10.1.2.11 (10.1.2.11), 30 hops max, 60 byte packets
 1  10.1.1.254 (10.1.1.254)  0.975 ms  0.936 ms  0.883 ms
 2  10.1.2.11 (10.1.2.11)  0.864 ms !X  0.766 ms !X  0.750 ms !X
```

## II. Interlude accès internet

☀️ Sur router.tp2

```powershell
[diane@router.tp2 ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=113 time=34.7 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=113 time=24.5 ms
^C
--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 24.486/29.606/34.727/5.120 ms
[diane@router.tp2 ~]$ ping google.com
PING google.com (216.58.214.78) 56(84) bytes of data.
64 bytes from par10s39-in-f14.1e100.net (216.58.214.78): icmp_seq=1 ttl=116 time=25.1 ms
64 bytes from par10s39-in-f14.1e100.net (216.58.214.78): icmp_seq=2 ttl=116 time=25.3 ms
^C
--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 25.096/25.173/25.251/0.077 ms
```

☀️ Accès internet LAN1 et LAN2

```powershell
[diane@node2.lan1.tp2 ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=112 time=23.2 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=112 time=23.2 ms
^C
--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 23.156/23.162/23.168/0.006 ms
[diane@node2.lan1.tp2 ~]$ ping google.com
PING google.com (216.58.214.78) 56(84) bytes of data.
64 bytes from par10s39-in-f14.1e100.net (216.58.214.78): icmp_seq=1 ttl=116 time=25.1 ms
64 bytes from par10s39-in-f14.1e100.net (216.58.214.78): icmp_seq=2 ttl=116 time=25.3 ms
^C
--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 25.096/25.173/25.251/0.077 ms
```

## III. Services réseau

### Web web web

```
[diane@web.lan2.tp2 ~]$ sudo dnf -y install nginx
[diane@web.lan2.tp2 ~]$ sudo nano /var/www/site_nul/index.html
meow
[diane@web.lan2.tp2 ~]$ sudo cat /etc/nginx/conf.d/site_nul.conf
server {
    listen 80;
    location / {
        root /var/www/site_nul/;
        index index.html index.htm;
    }
}
[diane@web.lan2.tp2 ~]$ sudo systemctl start nginx
[diane@web.lan2.tp2 ~]$ sudo systemctl enable nginx
[diane@web.lan2.tp2 var]$ sudo firewall-cmd --add-port=80/tcp --permanent
[diane@web.lan2.tp2 var]$ sudo firewall-cmd --reload
[diane@web.lan2.tp2 site_nul]$ ss -alntupe | grep 80
tcp   LISTEN 0      511          0.0.0.0:80        0.0.0.0:*    ino:23440 sk:4 cgroup:/system.slice/nginx.service <->
tcp   LISTEN 0      511             [::]:80           [::]:*    ino:23441 sk:6 cgroup:/system.slice/nginx.service v6only:1 <->
[diane@web.lan2.tp2 ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3
  sources:
  services: cockpit dhcpv6-client ssh
  ports: 80/tcp
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```

☀️ Sur node1.lan1.tp

```powershell
[diane@web.lan2.tp2 var]$ curl site_nul.tp2
meow
```
