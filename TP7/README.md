# TP7 : Do u secure

## SSH

### Fingerprint

🌞 Effectuez une connexion SSH en vérifiant le fingerprint

```powershell
[diane@john ~]$ sudo ssh-keygen -l -f /etc/ssh/ssh_host_ed25519_key
256 SHA256:cafbLpSksNa81mgbdf9egE/jp1AC5rnttm+bzRdRn8o /etc/ssh/ssh_host_ed25519_key.pub (ED25519)
```

```powershell
PS C:\Users\Diane> ssh diane@10.7.1.11
The authenticity of host '10.7.1.11 (10.7.1.11)' can't be established.
ED25519 key fingerprint is SHA256:cafbLpSksNa81mgbdf9egE/jp1AC5rnttm+bzRdRn8o.
This host key is known by the following other names/addresses:
    C:\Users\Diane/.ssh/known_hosts:25: 10.3.1.11
    C:\Users\Diane/.ssh/known_hosts:28: 10.3.1.12
    C:\Users\Diane/.ssh/known_hosts:29: 10.3.2.12
    C:\Users\Diane/.ssh/known_hosts:30: 10.3.2.12
    C:\Users\Diane/.ssh/known_hosts:31: 10.3.2.254
    C:\Users\Diane/.ssh/known_hosts:32: 10.3.1.254
    C:\Users\Diane/.ssh/known_hosts:33: 10.4.1.12
    C:\Users\Diane/.ssh/known_hosts:34: 10.4.1.254
    (11 additional names omitted)
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.7.1.11' (ED25519) to the list of known hosts.
diane@10.7.1.11's password:
Last login: Thu Nov 23 10:58:55 2023 from 10.7.1.1
```

🌞 Consulter l'état actuel

```powershell
[diane@router ~]$ sudo cat /etc/ssh/sshd_config
...
# If you want to change the port on a SELinux system, you have to tell
# SELinux about this change.
# semanage port -a -t ssh_port_t -p tcp #PORTNUMBER
#
#Port 22
...
```
