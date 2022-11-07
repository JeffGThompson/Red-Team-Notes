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
