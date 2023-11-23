# Biohazard

**Room Link:** [https://tryhackme.com/room/biohazard](https://tryhackme.com/room/biohazard)

## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Loot



| Flag                                           | Found in                                                                                                                                                   | Used in           |
| ---------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------- |
| emblem{fec832623ea498e20bf4fe1821d58727}       | [http://10.10.225.236/diningRoom/emblem.php](http://10.10.225.236/diningRoom/emblem.php)                                                                   |                   |
| lock\_pick{037b35e2ff90916a9abf99129c8e1837}   | [http://10.10.225.236/teaRoom/master\_of\_unlock.html](http://10.10.225.236/teaRoom/master\_of\_unlock.html)                                               | Used in /barRoom/ |
| music\_sheet{362d72deaf65f5bdc63daece6a1f676e} | [http://10.10.225.236/barRoom357162e3db904857963e6e0b64b96ba7/musicNote.html](http://10.10.225.236/barRoom357162e3db904857963e6e0b64b96ba7/musicNote.html) |                   |
|                                                |                                                                                                                                                            |                   |
|                                                |                                                                                                                                                            |                   |
|                                                |                                                                                                                                                            |                   |
|                                                |                                                                                                                                                            |                   |



## Rooms found

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

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



## TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>









<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>







<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

