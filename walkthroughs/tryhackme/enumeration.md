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

<figure><img src="../../.gitbook/assets/image (6) (10).png" alt=""><figcaption></figcaption></figure>



**What is the name of the user who last logged in to the system?**

**Victim**

```
last
```

<figure><img src="../../.gitbook/assets/image (1) (4).png" alt=""><figcaption></figcaption></figure>

**What is the highest listening TCP port number?**

**Victim**

```
sudo netstat -atpn
```

<figure><img src="../../.gitbook/assets/image (11) (2).png" alt=""><figcaption></figcaption></figure>

**What is the program name of the service listening on it?**

**Victim**

```
sudo netstat -atpn
```

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

\
**There is a script running in the background. Its name starts with `THM`. What is the name of the script?**

**Victim**

```
ps axf
```

<figure><img src="../../.gitbook/assets/image (2) (10).png" alt=""><figcaption></figcaption></figure>

## Windows Enumeration

**What is the full OS Name?**

**Victim**

```
systeminfo
```

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

**What is the OS Version?**

**Victim**

```
systeminfo
```

<figure><img src="../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

**How many hotfixes are installed on this MS Windows Server?**

**Victim**

```
systeminfo
```

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

**What is the lowest TCP port number listening on the system?**

**Victim**

```
netstat -n
```

****![](<../../.gitbook/assets/image (6) (3).png>)****

**What is the name of the program listening on that port?**

**Victim**

```
netstat -nob
```

<figure><img src="../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

## DNS, SMB, and SNMP

\
**Knowing that the domain name on the MS Windows Server of IP `10.10.100.178` is `redteam.thm`, use `dig` to carry out a domain transfer. What is the flag that you get in the records?**

**Kali**

```
dig -t AXFR redteam.thm @10.10.100.178
```

<figure><img src="../../.gitbook/assets/image (2) (2).png" alt=""><figcaption></figcaption></figure>

\
**What is the name of the share available over SMB protocol and starts with `THM`?**

**Victim**

```
net share
```

<figure><img src="../../.gitbook/assets/image (1) (13).png" alt=""><figcaption></figcaption></figure>

**Knowing that the community string used by the SNMP service is `public`, use `snmpcheck` to collect information about the MS Windows Server of IP `10.10.100.178`. What is the location specified?**

**Kali**

```
git clone https://gitlab.com/kalilinux/packages/snmpcheck.git
cd snmpcheck/
gem install snmp
chmod +x snmpcheck-1.9.rb
./snmpcheck-1.9.rb 10.10.100.178 -c public
```

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>
