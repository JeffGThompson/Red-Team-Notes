# Biohazard

**Room Link:** [https://tryhackme.com/room/biohazard](https://tryhackme.com/room/biohazard)

## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Loot



| Flag                                           | Found in                                                                                                                                                   | Used in           |
| ---------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------- |
| emblem{fec832623ea498e20bf4fe1821d58727}       | [http://10.10.225.236/diningRoom/emblem.php](http://10.10.225.236/diningRoom/emblem.php)                                                                   |                   |
| lock\_pick{037b35e2ff90916a9abf99129c8e1837}   | [http://10.10.225.236/teaRoom/master\_of\_unlock.html](http://10.10.225.236/teaRoom/master\_of\_unlock.html)                                               | Used in /barRoom/ |
| music\_sheet{362d72deaf65f5bdc63daece6a1f676e} | [http://10.10.225.236/barRoom357162e3db904857963e6e0b64b96ba7/musicNote.html](http://10.10.225.236/barRoom357162e3db904857963e6e0b64b96ba7/musicNote.html) |                   |
| blue\_jewel{e1d457e96cac640f863ec7bc475d48aa}  | [http://10.10.113.215/diningRoom/sapphire.html](http://10.10.113.215/diningRoom/sapphire.html)                                                             |                   |
|                                                |                                                                                                                                                            |                   |
|                                                |                                                                                                                                                            |                   |
|                                                |                                                                                                                                                            |                   |



## Rooms found

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
/diningRoom/
/teaRoom/
/artRoom/
/barRoom/
/diningRoom2F/
/tigerStatusRoom/
/galleryRoom/
/studyRoom/
/armorRoom/
/attic/
```

Used this script to loop through the rooms to quickly look for clues

**search.sh**

```
#!/bin/bash

# List of rooms
rooms=(
  "/diningRoom/"
  "/teaRoom/"
  "/artRoom/"
  "/barRoom/"
  "/diningRoom2F/"
  "/tigerStatusRoom/"
  "/galleryRoom/"
  "/studyRoom/"
  "/armorRoom/"
  "/attic/"
)

# Base URL
base_url="http://$VICTIM"  # Replace with the actual base URL

# Loop through each room and curl the site
for room in "${rooms[@]}"; do
  url="$base_url$room"
  echo "Curling: $url"
  curl "$url"
  echo -e "\n----------------------------------------\n"
done

```

## TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>









## Mansion Main

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## diningRoom

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>







<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## diningRoom2F

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



## diningRoom

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>





## tigerStatusRoom

I put the blue gem flag in here

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Crest 1 : S0pXRkVVS0pKQkxIVVdTWUpFM0VTUlk9



## galleryRoom

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Crest 2 : GVFWK5KHK5WTGTCILE4DKY3DNN4GQQRTM5AVCTKE



## barRoom

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (15) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Using the other emblem we get this

<figure><img src="../../.gitbook/assets/image (17) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## diningRoom

Used the gold emblem here

<figure><img src="../../.gitbook/assets/image (18) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (19) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (20) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (21) (1).png" alt=""><figcaption></figcaption></figure>

## attic

<figure><img src="../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

Crest 4 : gSUERauVpvKzRpyPpuYz66JDmRTbJubaoArM6CAQsnVwte6zF9J4GGYyun3k5qM9ma4s



## armorRoom

<figure><img src="../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

Crest 3: MDAxMTAxMTAgMDAxMTAwMTEgMDAxMDAwMDAgMDAxMTAwMTEgMDAxMTAwMTEgMDAxMDAwMDAgMDAxMTAxMDAgMDExMDAxMDAgMDAxMDAwMDAgMDAxMTAwMTEgMDAxMTAxMTAgMDAxMDAwMDAgMDAxMTAxMDAgMDAxMTEwMDEgMDAxMDAwMDAgMDAxMTAxMDAgMDAxMTEwMDAgMDAxMDAwMDAgMDAxMTAxMTAgMDExMDAwMTEgMDAxMDAwMDAgMDAxMTAxMTEgMDAxMTAxMTAgMDAxMDAwMDAgMDAxMTAxMTAgMDAxMTAxMDAgMDAxMDAwMDAgMDAxMTAxMDEgMDAxMTAxMTAgMDAxMDAwMDAgMDAxMTAwMTEgMDAxMTEwMDEgMDAxMDAwMDAgMDAxMTAxMTAgMDExMDAwMDEgMDAxMDAwMDAgMDAxMTAxMDEgMDAxMTEwMDEgMDAxMDAwMDAgMDAxMTAxMDEgMDAxMTAxMTEgMDAxMDAwMDAgMDAxMTAwMTEgMDAxMTAxMDEgMDAxMDAwMDAgMDAxMTAwMTEgMDAxMTAwMDAgMDAxMDAwMDAgMDAxMTAxMDEgMDAxMTEwMDAgMDAxMDAwMDAgMDAxMTAwMTEgMDAxMTAwMTAgMDAxMDAwMDAgMDAxMTAxMTAgMDAxMTEwMDA=





## Crest

Crest part was not hard, just dumped them all into cyberchef which was able to decode it automatically, then put them all together to reveal the FTP account

<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>



## TCP/21 - FTP

**Kali**

```
ftp $VICTIM
Username: hunter
Password: you_cant_hide_forever
mget *
```

<figure><img src="../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
steghide extract -sf 001-key.jpg 
cat key-001.txt 
```

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
exiftool 002-key.jpg 
```

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
binwalk 003-key.jpg -e
cat _003-key.jpg.extracted/key-003.txt 
```

<figure><img src="../../.gitbook/assets/image (33).png" alt=""><figcaption></figcaption></figure>



Combining the three get us this

<figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
gpg --output doc --decrypt helmet_key.txt.gpg
Password: plant42_can_be_destroy_with_vjolt
cat doc
```

<figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

##

## hidden\_closet

<figure><img src="../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>



Used Vignere cipher again, except we didn't have the key so I bruteforced it, it was albert.

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

## studyRoom

<figure><img src="../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
tar xvf doom.tar.gz 
cat eagle_medal.txt 
```

<figure><img src="../../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>





## TCP/22 - SSH

**Kali**

```
ssh umbrella_guest@$VICTIM
Password: T_virus_rules
```

<figure><img src="../../.gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh weasker@$VICTIM
Password: stars_members_are_my_guinea_pig
```

<figure><img src="../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
sudo -i
```

<figure><img src="../../.gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (500).png" alt=""><figcaption></figcaption></figure>













