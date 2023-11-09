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
