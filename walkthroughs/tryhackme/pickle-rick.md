# Pickle Rick

**Room Link:** [https://tryhackme.com/room/picklerick](https://tryhackme.com/room/picklerick)



## Scanning

### Initial Scan

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found.

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>



### HTTP port 80

```
dirb http://$VICTIM:80 /usr/share/wordlists/dirb/big.txt
```

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

Found Ricks username in the page source of the main page

<figure><img src="../../.gitbook/assets/image (2) (2) (4).png" alt=""><figcaption></figcaption></figure>

robots.txt just had this, could be a password.

<figure><img src="../../.gitbook/assets/image (18) (2).png" alt=""><figcaption></figcaption></figure>

Ran the same dirb scan again except looking for .php files, I was able to find some pages.

```
dirb http://$VICTIM:80 /usr/share/wordlists/dirb/big.txt -X .php
```

<figure><img src="../../.gitbook/assets/image (4) (2) (3).png" alt=""><figcaption></figcaption></figure>

```
Website: http://$VICTIM/login.php
Username: R1ckRul3s
Password: Wubbalubbadubdub
```

<figure><img src="../../.gitbook/assets/image (22) (5).png" alt=""><figcaption></figcaption></figure>

Login worked

<figure><img src="../../.gitbook/assets/image (6) (5).png" alt=""><figcaption></figcaption></figure>

First ingredient found, also tried doing a reverse shell with netcat but not working

<figure><img src="../../.gitbook/assets/image (6) (2).png" alt=""><figcaption></figcaption></figure>

Clue to look around.

<figure><img src="../../.gitbook/assets/image (23) (1) (3).png" alt=""><figcaption></figcaption></figure>

Second ingrediant found

<figure><img src="../../.gitbook/assets/image (21) (3).png" alt=""><figcaption></figcaption></figure>

www-data can actually run any command with sudo without entering a password.&#x20;

<figure><img src="../../.gitbook/assets/image (1) (6) (3).png" alt=""><figcaption></figcaption></figure>

The last ingredient is found in the root directory.

<figure><img src="../../.gitbook/assets/image (15) (3).png" alt=""><figcaption></figcaption></figure>

### SSH port 22

Tried logging in with the username and potential password we found but ssh fails right away before entering a password.

```
ssh R1ckRul3s@$VICTIM

Username: R1ckRul3s
Password: Wubbalubbadubdub
```

<figure><img src="../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>
