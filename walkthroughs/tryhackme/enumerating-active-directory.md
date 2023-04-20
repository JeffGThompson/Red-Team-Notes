# Enumerating Active Directory

**Room Link:** [https://tryhackme.com/room/adenumeration](https://tryhackme.com/room/adenumeration)

## Why AD Enumeration

**Kali**

```
systemd-resolve --interface breachad --set-dns $THMDCIP --set-domain za.tryhackme.com
nslookup thmdc.za.tryhackme.com
```

## Enumeration through Microsoft Management Console

**Kali**

```
xfreerdp +clipboard /u:"mandy.bryan" /v:thmjmp1.za.tryhackme.com:3389 /size:1024x568 /smart-sizing:800x1200
Password: Dbrnjhbz1986
```







