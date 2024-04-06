# battery

**Room Link:** [https://tryhackme.com/r/room/battery ](https://tryhackme.com/r/room/battery)



### **Scans** <a href="#scans" id="scans"></a>

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (890).png" alt=""><figcaption></figcaption></figure>



Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (891).png" alt=""><figcaption></figcaption></figure>

## **TCP/80 - HTTP**

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (892).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
gobuste
```















<figure><img src="../../.gitbook/assets/image (893).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
cat report 
```

<figure><img src="../../.gitbook/assets/image (894).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
strings report 
```

<figure><img src="../../.gitbook/assets/image (895).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (896).png" alt=""><figcaption></figcaption></figure>



The username field has a character limit

<figure><img src="../../.gitbook/assets/image (897).png" alt=""><figcaption></figcaption></figure>

The original request. It will fail to register.

<figure><img src="../../.gitbook/assets/image (900).png" alt=""><figcaption></figcaption></figure>

We can just add a random character to the end which will cut off and then it will register the user.

<figure><img src="../../.gitbook/assets/image (901).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (902).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (903).png" alt=""><figcaption></figcaption></figure>











