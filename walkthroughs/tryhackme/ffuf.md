# ffuf

**Room Link:** [https://tryhackme.com/room/ffuf](https://tryhackme.com/room/ffuf)



## Basics

**Kali**

```
ffuf -u http://$VICTIM/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt 
```

You could also use any custom keyword instead of `FUZZ`, you just need to define it like this `wordlist.txt:KEYWORD`.

**Kali**

```
ffuf -u http://$VICTIM/NORAJ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/big.txt:NORAJ
```

<figure><img src="../../.gitbook/assets/image (812).png" alt=""><figcaption></figcaption></figure>

## Finding pages and directories

One approach you could take would be to start enumerating with a generic list of files such as raft-medium-files-lowercase.txt.

### Find pages

**Kali**

```
ffuf -u http://$VICTIM/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-files-lowercase.txt
```

### Find pages with certain extensions

However, using a large generic wordlist containing irrelevant file extensions is not very efficient.

Instead, we can usually assume `index.<extension>` is the default page on most websites so we can try common extensions for just the index page. With this method, we can usually determine what programming language or languages the site uses.

For example, we can append the extension after index.

**Kali**

```
head /usr/share/wordlists/SecLists/Discovery/Web-Content/web-extensions.txt  
```

**web-extensions.txt**

```
.asp
.aspx
.bat
.c
.cfm
.cgi
.css
.com
.dll
.exe
```



**Kali**

```
ffuf -u http://$VICTIM/indexFUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/web-extensions.txt  
```



### Find pages and exclude certain extensions

Now that we know the extensions supported we can try a list of generic words (without of extension) and apply the extensions we know works (found from Q2) + some common ones like `.txt`.

We'll exclude the 4 letter extensions from this wordlist as it will result in many false positives.



**Kali**

```
ffuf -u http://$VICTIM/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-words-lowercase.txt -e .php,.txt
```

### &#x20;Find Directories&#x20;

Directory names are not always dependent on the type of environment you're enumerating and is often a good starting point before attempting to fuzz for files. If we wanted to fuzz directories we only need to provide a wordlist.

**Kali**

```
ffuf -u http://$VICTIM/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories-lowercase.txt
```



## Using filters

Remember the command we ran for Q1 of Task 3?

We had a lot of output but not much was useful.\
For example, a 403 [HTTP status code](https://en.wikipedia.org/wiki/List\_of\_HTTP\_status\_codes) indicates that we're forbidden to access the requested resource. Let's hide responses with 403 status codes for now. We can accomplish this by using filters.

By adding `-fc 403` (filter code) we'll hide from the output all 403 [HTTP status codes](https://en.wikipedia.org/wiki/List\_of\_HTTP\_status\_codes).\
\


**Kali**

```
ffuf -u http://$VICTIM/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-files-lowercase.txt -fc 403
```

<figure><img src="../../.gitbook/assets/image (813).png" alt=""><figcaption></figcaption></figure>

Sometimes you might want to filter out multiple status codes such as 500, 302, 301, 401, etc. For instance, if you know you want to see 200 status code responses, you could use `-mc 200` (match code) instead of having a long list of filtered codes.



**Kali**

```
ffuf -u http://$VICTIM/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-files-lowercase.txt -mc 200
```

<figure><img src="../../.gitbook/assets/image (814).png" alt=""><figcaption></figcaption></figure>

Sometimes it might be beneficial to see what requests the server doesn't handle by matching for HTTP 500 Internal Server Error response codes (`-mc 500`). Finding irregularities in behavior could help better understand how the web app works.\
\
There are other filters and matchers. For example, you could encounter entries with a 200 status code with a response size of zero. eg. `functions.php` or `inc/myfile.php`.\
\


Unless we have a LFI (local file inclusion) this kind of files aren't interesting, so we can use `-fs 0` (filter size).

Here are all filters and matchers:\


```
$ ffuf -h
...
MATCHER OPTIONS:
  -mc                 Match HTTP status codes, or "all" for everything. (default: 200,204,301,302,307,401,403,405)
  -ml                 Match amount of lines in response
  -mr                 Match regexp
  -ms                 Match HTTP response size
  -mw                 Match amount of words in response

FILTER OPTIONS:
  -fc                 Filter HTTP status codes from response. Comma separated list of codes and ranges
  -fl                 Filter by amount of lines in response. Comma separated list of line counts and ranges
  -fr                 Filter regexp
  -fs                 Filter HTTP response size. Comma separated list of sizes and ranges
  -fw                 Filter by amount of words in response. Comma separated list of word counts and ranges
```

We often see there are false positives with files beginning with a dot (eg. `.htgroups`,`.php`, etc.). They throw a 403 Forbidden error, however those files don't actually exist. It's tempting to use `-fc 403` but this could hide valuable files we don't have access to yet. So instead we can use a regexp to match all files beginning with a dot.

**Kali**

```
ffuf -u http://$VICTIM/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-files-lowercase.txt -fr '/\..*'
```

<figure><img src="../../.gitbook/assets/image (815).png" alt=""><figcaption></figcaption></figure>

## Fuzzing parameters

What would you do when you find a page or API endpoint but don't know which parameters are accepted? You fuzz!

Discovering a vulnerable parameter could lead to file inclusion, path disclosure, XSS, SQL injection, or even command injection. Since ffuf allows you to put the keyword anywhere we can use it to fuzz for parameters.

**Kali**

```
ffuf -u http://$VICTIM/sqli-labs/Less-1/?FUZZ=1 -c -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-words-lowercase.txt -fw 39
```

<figure><img src="../../.gitbook/assets/image (816).png" alt=""><figcaption></figcaption></figure>

Now that we found a parameter accepting integer values we'll start fuzzing values.

At this point, we could generate a wordlist and save a file containing integers. To cut out a step we can use `-w -` which tells ffuf to read a wordlist from [stdout](https://www.gnu.org/software/libc/manual/html\_node/Standard-Streams.html). This will allow us to generate a list of integers with a command of our choice then pipe the output to ffuf. Below is a list of 5 different ways to generate numbers 0 - 255.



### Option #1

**Kali**

```
ruby -e '(0..255).each{|i| puts i}' | ffuf -u http://$VICTIM/sqli-labs/Less-1/?id=FUZZ -c -w - -fw 33
```

<figure><img src="../../.gitbook/assets/image (817).png" alt=""><figcaption></figcaption></figure>

### Option #2

**Kali**

```
ruby -e 'puts (0..255).to_a' | ffuf -u http://$VICTIM/sqli-labs/Less-1/?id=FUZZ -c -w - -fw 33
```

<figure><img src="../../.gitbook/assets/image (818).png" alt=""><figcaption></figcaption></figure>

### Option #3

**Kali**

```
for i in {0..255}; do echo $i; done | ffuf -u http://$VICTIM/sqli-labs/Less-1/?id=FUZZ -c -w - -fw 33
```

<figure><img src="../../.gitbook/assets/image (819).png" alt=""><figcaption></figcaption></figure>

### Option #4

**Kali**

```
seq 0 255 | ffuf -u http://$VICTIM/sqli-labs/Less-1/?id=FUZZ -c -w - -fw 33
```

<figure><img src="../../.gitbook/assets/image (820).png" alt=""><figcaption></figcaption></figure>

\
We can also use ffuf for wordlist-based brute-force attacks, for example, trying passwords on an authentication page.

**Kali**

```
ffuf -u http://$VICTIM/sqli-labs/Less-11/ -c -w /usr/share/wordlists/SecLists/Passwords/Leaked-Databases/hak5.txt -X POST -d 'uname=Dummy&passwd=FUZZ&submit=Submit' -fs 1435 -H 'Content-Type: application/x-www-form-urlencoded'
```

<figure><img src="../../.gitbook/assets/image (821).png" alt=""><figcaption></figcaption></figure>

Here we have to use the POST method (specified with `-X`) and to give the POST data (with `-d`) where we include the `FUZZ` keyword in place of the password.

We also have to specify a custom header `-H 'Content-Type: application/x-www-form-urlencoded'` because ffuf doesn't set this content-type header automatically as curl does.



## Finding vhosts and subdomains

ffuf may not be as efficient as specialized tools when it comes to subdomain enumeration but it's possible to do.

**Kali**

```
ffuf -u http://FUZZ.mydomain.com -c -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
```

<figure><img src="../../.gitbook/assets/image (822).png" alt=""><figcaption></figcaption></figure>

Some subdomains might not be resolvable by the DNS server you're using and are only resolvable from within the target's local network by their private DNS servers. So some virtual hosts (vhosts) may exist with private subdomains so the previous command doesn't find them. To try finding private subdomains we'll have to use the Host HTTP header as these requests might be accepted by the web server.\
Note: [virtual hosts](https://httpd.apache.org/docs/2.4/en/vhosts/examples.html) (vhosts) is the name used by Apache httpd but for Nginx the right term is [Server Blocks](https://www.nginx.com/resources/wiki/start/topics/examples/server\_blocks/).\


You could compare the results obtained with direct subdomain enumeration and with vhost enumeration:

**Kali**

```
ffuf -u http://FUZZ.mydomain.com -c -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -fs 0
```

<figure><img src="../../.gitbook/assets/image (823).png" alt=""><figcaption></figcaption></figure>

**Kali(using Vhost)**

```
ffuf -u http://mydomain.com -c -w /usr/share/wordlists/SecLists/Discovery/DNS/subdomains-top1million-5000.txt -H 'Host: FUZZ.mydomain.com' -fs 0
```

<figure><img src="../../.gitbook/assets/image (824).png" alt=""><figcaption></figcaption></figure>

For example, it is possible that you can't find a sub-domain with direct subdomain enumeration (1st command) but that you can find it with vhost enumeration (2nd command).

Vhost enumeration technique shouldn't be discounted as it may lead to discovering content that wasn't meant to be accessed externally.

## Proxifying ffuf traffic

Whether it' for [network pivoting](https://blog.raw.pm/en/state-of-the-art-of-network-pivoting-in-2019/) or for using BurpSuite plugins you can send all the ffuf traffic through a web proxy (HTTP or SOCKS5).

**Kali**

```
ffuf -u http://$VICTIM -c -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -x http://127.0.0.1:8080
```

It's also possible to send only matches to your proxy for replaying:

**Kali**

```
ffuf -u http://$VICTIM  -c -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -replay-proxy http://127.0.0.1:8080
```

This may be useful if you don't need all the traffic to traverse an upstream proxy and want to minimize resource usage or to avoid polluting your proxy history.

## Reviewing the options

**How do you save the output to a markdown file (ffuf.md)?**

```
// Some code
```

**How do you re-use a raw http request file?**

<pre><code><strong>-request
</strong></code></pre>

**How do you strip comments from a wordlist?**

```
-ic
```

**How would you read a wordlist from STDIN?**

```
-w -
```

**How do you print full URLs and redirect locations?**

```
-v
```

**What option would you use to follow redirects?**

```
-r
```

**How do you enable colorized output?**

```
-c
```
