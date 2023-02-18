# Common Linux Privesc

**Room Link:** [https://tryhackme.com/room/commonlinuxprivesc](https://tryhackme.com/room/commonlinuxprivesc)



## Enumeration



Download LinEnum Script

**Kali**

```
wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
```

Login to host

**Kali**

```
ssh user3@$VICTIM
Password: password
```

The target's hostname

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

There are 8 users

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

4 shells

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Some users can write to passwd file

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## Abusing SUID/GUID Files

