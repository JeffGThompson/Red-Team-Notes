# Enumeration

**Room Link:** [https://tryhackme.com/room/enumerationpe](https://tryhackme.com/room/enumerationpe)



## Linux Enumeration



**What is the Linux distribution used in the VM?**

**Victim**

```
cat /etc/*-release
```

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

**What is its version number?**

**Victim**

```
cat /etc/*-release
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>



**What is the name of the user who last logged in to the system?**

**Victim**

```
last
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**What is the highest listening TCP port number?**

**Victim**

```
sudo netstat -atpn
```

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

**What is the program name of the service listening on it?**

**Victim**

```
sudo netstat -atpn
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

\
**There is a script running in the background. Its name starts with `THM`. What is the name of the script?**

**Victim**

```
ps axf
```

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>
