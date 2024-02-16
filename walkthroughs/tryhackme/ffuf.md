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























