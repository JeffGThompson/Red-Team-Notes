# The Great Escape

**Room Link:** [https://tryhackme.com/r/room/thegreatescape](https://tryhackme.com/r/room/thegreatescape)



## Initial Scan <a href="#initial-scan" id="initial-scan"></a>

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>



## Scan all ports <a href="#scan-all-ports" id="scan-all-ports"></a>

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

### TCP/80 - HTTP <a href="#tcp-8080-http" id="tcp-8080-http"></a>

Its returning too much from 200 so we need to filter it out

**Kali**

```
gobuster -e dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt --wildcard
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

**Kali**

Had to add .well-known to the wordlist, wasn't in any of Tryhackme's default wordlists

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/dirb/common.txt  --wildcard  -s"204,301,302,307,401,403"
```

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

None of this was working, try later.

**Kali**

```
dirb http://$VICTIM/.well-known -X .txt
```

**Kali**

```
curl http://$VICTIM/.well-known/security.txt
```

**Kali**

```
curl http://$VICTIM/api/fl46
```

**Kali**

```
curl http://$VICTIM/robots.txt
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>







<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>















