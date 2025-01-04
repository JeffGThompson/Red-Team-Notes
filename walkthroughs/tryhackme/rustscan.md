# RustScan

**Room Link:** [https://tryhackme.com/r/room/rustscan](https://tryhackme.com/r/room/rustscan)



## Scanning Time!

The tool is really amazing in terms of scanning. It can scan all the ports really fast and then pipe the output to the Nmap. Now in this room, we'll scan our vulnerable machine.\
Basic format for RustScan is rustscan -r ports -a  \<Target-ip> -- \<nmap cmds>\
\
Here's a full list of things you can do.

### Multiple IP Scanning

You can scan multiple IPs using a comma-separated list like so:

```
rustscan -a 127.0.0.1,0.0.0.0
```

### Host Scanning

RustScan can also scan hosts, like so:

```
➜ rustscan -a www.google.com, 127.0.0.1
Open 216.58.210.36:1
Open 216.58.210.36:80
Open 216.58.210.36:443
Open 127.0.0.1:53
Open 127.0.0.1:631
```

### CIDR support

RustScan supports CIDR:

```
➜ rustscan -a 192.168.0.0/30
```

### Hosts file as input

The file is a new line separated list of IPs / Hosts to scan:

hosts.txt

```
192.168.0.1
192.168.0.2
google.com
192.168.0.0/30
127.0.0.1
```

The argument is:

```
rustscan -a 'hosts.txt'
```

### Individual Port Scanning

RustScan can scan individual ports, like so:

```
➜ rustscan -a 127.0.0.1 -p 53
53
```

### Multiple selected port scanning

You can input a comma-separated list of ports to scan:

```
➜ rustscan -a 127.0.0.1 -p 53,80,121,65535
53
```

### Ranges of ports

To scan a range of ports:

To run:

```
➜ rustscan -a 127.0.0.1 --range 1-1000    
53,631
```

### Adjusting the Nmap arguments

RustScan, at the moment, runs Nmap by default.

You can adjust the arguments like so:

```
rustscan -a 127.0.0.1 -- -A -sC
```

To run:

```
nmap -Pn -vvv -p $PORTS -A -sC 127.0.0.1
```

### Random Port Ordering

If you want to scan ports in a random order (which will help with not setting off firewalls) run RustScan like this:

```
➜ rustscan -a 127.0.0.1 --range 1-1000 --scan-order "Random"
53,631
```

### &#x20;Answer the questions

**After scanning this, how many ports do we find open under 1000?**

**Kali**

```
rustscan -a $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
2
```

**Perform a service version detection scan, what is the version of the software running on port 22?**

**Kali**

```
rustscan -a $VICTIM  -p 22 -- -A -sC
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
6.6.1p1
```

**Perform an aggressive scan, what flag isn't set under the results for port 80?**

**Kali**

```
rustscan -a $VICTIM  -p 80 -- -A -sC
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
httponly
```
