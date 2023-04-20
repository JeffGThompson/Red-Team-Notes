# Enumerating Active Directory

**Room Link:** [https://tryhackme.com/room/adenumeration](https://tryhackme.com/room/adenumeration)

## Why AD Enumeration

**Kali**

```
systemd-resolve --interface breachad --set-dns $THMDCIP --set-domain za.tryhackme.com
nslookup thmdc.za.tryhackme.com
```

