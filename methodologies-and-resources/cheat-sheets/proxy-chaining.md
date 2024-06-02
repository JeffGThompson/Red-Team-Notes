# Proxy Chaining

## SSH Proxy Chaining

**Examples**

[game-zone.md](../../walkthroughs/tryhackme/game-zone.md "mention")

Allows us to gain access to the service running on port 10000 from Kali that was only accessible to the victim from their machine.

```
ssh -L 10000:localhost:10000 $USERNAME@$VICTIM
Password: $ASSWORD
```

