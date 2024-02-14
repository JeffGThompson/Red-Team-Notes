# ffuf

**Room Link:** [https://tryhackme.com/room/ffuf](https://tryhackme.com/room/ffuf)



## Basics

ffuf -u http://MACHINE\_IP/FUZZ -w /usr/share/seclists/Discovery/Web-Content/big.txt

**Kali**

```
ffuf -u http://$VICTIM/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt 
```

You could also use any custom keyword instead of `FUZZ`, you just need to define it like this `wordlist.txt:KEYWORD`.

**Kali**

```
ffuf -u http://$VICTIM/NORAJ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt:NORAJ
```

<figure><img src="../../.gitbook/assets/image (812).png" alt=""><figcaption></figcaption></figure>



