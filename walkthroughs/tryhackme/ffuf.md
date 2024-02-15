# ffuf

**Room Link:** [https://tryhackme.com/room/ffuf](https://tryhackme.com/room/ffuf)



## Basics

ffuf -u http://MACHINE\_IP/FUZZ -w /usr/share/seclists/Discovery/Web-Content/big.txt

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



































