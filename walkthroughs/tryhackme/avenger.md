# AVenger

**Room Link:** [https://tryhackme.com/r/room/avenger](https://tryhackme.com/r/room/avenger)

**Scans**

Initial scan

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (978).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (979).png" alt=""><figcaption></figcaption></figure>

Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (984).png" alt=""><figcaption></figcaption></figure>



### **TCP/80 - HTTP** <a href="#tcp-80-http" id="tcp-80-http"></a>

**Find Pages**

There were too many 404 and 403 in the scan so I changed to ffuf to ignore them.

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

**Kali**

```
ffuf -u http://$VICTIM/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -fc 404,403 -e .php,.html,.txt
```

<figure><img src="../../.gitbook/assets/image (981).png" alt=""><figcaption></figcaption></figure>









<figure><img src="../../.gitbook/assets/image (980).png" alt=""><figcaption></figcaption></figure>





The gift folder takes us to avenger.tryhackme

<figure><img src="../../.gitbook/assets/image (982).png" alt=""><figcaption></figcaption></figure>



**Add hostname to host file**

**Kali**

```
echo $VICTIM avenger.tryhackme  >> /etc/hosts
cat /etc/hosts
```

<figure><img src="../../.gitbook/assets/image (983).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ffuf -u http://avenger.tryhackme/gift/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -fc 404,403 -e .php,.html,.txt
```





**Kali**

```
wpscan --url http://avenger.tryhackme/wordpress
```

<figure><img src="../../.gitbook/assets/image (985).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (986).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
f
```



[http://avenger.tryhackme/gift/wp-content/plugins/forminator/](http://avenger.tryhackme/gift/wp-content/plugins/forminator/)





