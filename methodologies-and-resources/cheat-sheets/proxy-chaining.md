# Proxy Chaining

## SSH Proxy Chaining

**Examples**

[game-zone.md](../../walkthroughs/tryhackme/game-zone.md "mention")[internal.md](../../walkthroughs/tryhackme/internal.md "mention")

Allows us to gain access to the service running on port 10000 from Kali that was only accessible to the victim from their machine.

**Kali**

```
ssh -L 10000:localhost:10000 $USERNAME@$VICTIM
Password: $ASSWORD
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





