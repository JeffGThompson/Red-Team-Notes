# Obscure

**Room Link:** [https://tryhackme.com/r/room/obscured](https://tryhackme.com/r/room/obscured)



## **Scans** <a href="#scans" id="scans"></a>

Initial scan

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1053).png" alt=""><figcaption></figcaption></figure>

Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1054).png" alt=""><figcaption></figcaption></figure>



## **TCP/21 - FTP**

**Kali**

```
ftp $VICTIM 21
Username: anonymous
```



**Kali(ftp)**

```
binary
passive
cd pub
mget *
```

<figure><img src="../../.gitbook/assets/image (1055).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1056).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
echo $VICTIM antisoft.thm >> /etc/hosts
cat /etc/hosts
```



We find a function that checks if the password is equal to 971234596, if it is the program gives us the password.

**Kali**

```
ghidra
```

<figure><img src="../../.gitbook/assets/image (1057).png" alt=""><figcaption></figcaption></figure>





**Kali**

```
chmod +x password 
./password
971234596
```

<figure><img src="../../.gitbook/assets/image (1058).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (1060).png" alt=""><figcaption></figcaption></figure>

**Master Password**

```
SecurePassword123!
```

<figure><img src="../../.gitbook/assets/image (1061).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (1059).png" alt=""><figcaption></figcaption></figure>



#### Find Pages <a href="#find-pages" id="find-pages"></a>

**Kali**

```
gobu
```





