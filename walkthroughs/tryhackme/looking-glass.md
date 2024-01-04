# Looking Glass

**Room Link:** [https://tryhackme.com/room/lookingglass](https://tryhackme.com/room/lookingglass)

## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (651).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (652).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (653).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (654).png" alt=""><figcaption></figcaption></figure>

## Scan all ports

Same as the first scan, a lot of ssh ports open

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>







## TCP/22 - SSH

**Kali**

<figure><img src="../../.gitbook/assets/image (655).png" alt=""><figcaption></figcaption></figure>



**Kali**

```
nmap -A $VICTIM -oN results.txt
grep -oE '^[0-9]+/' results.txt > num.txt
cat num.txt
```

<figure><img src="../../.gitbook/assets/image (656).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
cat num.txt |  tr -d '\n' | sed 's/\//,/g' | tr -d ' '
```

<figure><img src="../../.gitbook/assets/image (658).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
for port in 9000 9001 9002 9003 9009 9010 9011 9040 9050 9071 9080 9081 9090 9091 9099 9100 9101 9102 9103 9110 9111 9200 9207 9220 9290 9415 9418 9485 9500 9502 9503 9535 9575 9593 9594 9595 9618 9666 9876 9877 9878 9898 9900 9917 9929 9943 9944 9968 9998 9999 10000 10001 10002 10003 10004 10009 10010 10012 10024 10025 10082 10180 10215 10243 10566 10616 10617 10621 10626 10628 10629 10778 11110 11111 11967 12000 12174 12265 12345 13456 13722 13782 13783; do
    echo "connecting to port $port"; ssh -o 'LogLevel=ERROR' -o 'StrictHostKeyChecking=no' -p $port test@$VICTIM;done | grep -vE 'Lower|Higher'
```



<figure><img src="../../.gitbook/assets/image (659).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
for i in $(seq 12345 13465); do echo "connecting to port $i"; ssh -o 'LogLevel=ERROR' -o 'StrictHostKeyChecking=no' -p $i $VICTIM;done | grep -vE 'Lower|Higher'
```

<figure><img src="../../.gitbook/assets/image (660).png" alt=""><figcaption></figcaption></figure>







```
Key: thealphabetcipher
```

<figure><img src="../../.gitbook/assets/image (661).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (662).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh $VICTIM -p 12350
Password: bewareTheJabberwock
```

<figure><img src="../../.gitbook/assets/image (663).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh jabberwock@$VICTIM 
Password: PlaceThanksSelfishGrinned
```



































