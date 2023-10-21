# Overpass

**Room Link:** [https://tryhackme.com/room/overpass](https://tryhackme.com/room/overpass)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (413).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (414).png" alt=""><figcaption></figcaption></figure>



### TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (415).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (416).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (417).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (418).png" alt=""><figcaption></figcaption></figure>

Click 'Resonse to this request' this will let us see the response and edit it.

<figure><img src="../../.gitbook/assets/image (419).png" alt=""><figcaption></figcaption></figure>





This is the response you get for an incorrect password

<figure><img src="../../.gitbook/assets/image (420).png" alt=""><figcaption></figcaption></figure>

I changed it forward to /admin instead

<figure><img src="../../.gitbook/assets/image (421).png" alt=""><figcaption></figcaption></figure>



Forward the request, disable FoxyProxy from the browser and see the page.

<figure><img src="../../.gitbook/assets/image (422).png" alt=""><figcaption></figcaption></figure>





### TCP/22 - SSH

**Kali**

```
/opt/john/ssh2john.py id_rsa.pub > id_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt id_john.txt 
```

<figure><img src="../../.gitbook/assets/image (423).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh james@$VICTIM -i id_rsa.pub 
Password: james13
```

<figure><img src="../../.gitbook/assets/image (424).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (425).png" alt=""><figcaption></figcaption></figure>







<figure><img src="../../.gitbook/assets/image (426).png" alt=""><figcaption></figcaption></figure>



Stopped here because I can't finish on Attackbox because port 80 is in use

















