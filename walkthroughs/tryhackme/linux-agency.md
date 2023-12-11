# Linux Agency

**Room Link:** [https://tryhackme.com/room/linuxagency](https://tryhackme.com/room/linuxagency)



## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (561).png" alt=""><figcaption></figcaption></figure>



## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (562).png" alt=""><figcaption></figcaption></figure>

## TCP/21 - SSH

**Kali**

```
ssh agent47@$VICTIM
Password: 640509040147
```

<figure><img src="../../.gitbook/assets/image (563).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
grep -r "mission" /home/ 2>/dev/null

su mission1
Password: mission1{174dc8f191bcbb161fe25f8a5b58d1f0}
```

<figure><img src="../../.gitbook/assets/image (564).png" alt=""><figcaption></figcaption></figure>

**Victim(mission1)**

```
cd /home/mission1
ls

su mission2
Password: mission2{8a1b68bb11e4a35245061656b5b9fa0d}
```

<figure><img src="../../.gitbook/assets/image (565).png" alt=""><figcaption></figcaption></figure>

**Victim(mission2)**

```
cd /home/mission2
cat flag.txt

su mission3
Password: mission3{ab1e1ae5cba688340825103f70b0f976}
```

<figure><img src="../../.gitbook/assets/image (566).png" alt=""><figcaption></figcaption></figure>

**Victim(mission3)**

```
cd /home/mission3
cat flag.txt
nano flag.txt

su mission4
Password: mission4{264a7eeb920f80b3ee9665fafb7ff92d}
```

<figure><img src="../../.gitbook/assets/image (568).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (567).png" alt=""><figcaption></figcaption></figure>

**Victim(mission4)**

```
cd /home/mission4/flag
cat flag.txt

su mission5
Password: mission5{bc67906710c3a376bcc7bd25978f62c0}
```

<figure><img src="../../.gitbook/assets/image (569).png" alt=""><figcaption></figcaption></figure>

**Victim(mission5)**

```
cd /home/mission4/
cat .flag.txt

su mission6
Password: mission6{1fa67e1adc244b5c6ea711f0c9675fde}
```

<figure><img src="../../.gitbook/assets/image (570).png" alt=""><figcaption></figcaption></figure>

**Victim(mission6)**

```
cd /home/mission5/
cat .flag/flag.txt 

su mission7
Password: mission7{53fd6b2bad6e85519c7403267225def5}
```

<figure><img src="../../.gitbook/assets/image (571).png" alt=""><figcaption></figcaption></figure>

**Victim(mission7)**

```
cd /home/mission7/
cat flag.txt 

su mission8
Password: mission8{3bee25ebda7fe7dc0a9d2f481d10577b}
```

**Victim(mission8)**

```
cd /
cat flag.txt 

su mission9
Password: mission9{ba1069363d182e1c114bef7521c898f5}
```

<figure><img src="../../.gitbook/assets/image (572).png" alt=""><figcaption></figcaption></figure>

**Victim(mission9)**

```
cd /home/mission8/
grep "mission10" rockyou.txt 

su mission10
Password: mission10{0c9d1c7c5683a1a29b05bb67856524b6}
```

<figure><img src="../../.gitbook/assets/image (573).png" alt=""><figcaption></figcaption></figure>

**Victim(mission10)**

```
cd /home/mission9/
grep -r "mission" . 2>/dev/null

su mission11
Password: mission11{db074d9b68f06246944b991d433180c0}
```

<figure><img src="../../.gitbook/assets/image (574).png" alt=""><figcaption></figcaption></figure>

**Victim(mission11)**

```
cd /home/mission11/
env

su mission12
Password: mission12{f449a1d33d6edc327354635967f9a720}
```

<figure><img src="../../.gitbook/assets/image (575).png" alt=""><figcaption></figcaption></figure>

**Victim(mission12)**

```
cd /home/mission12/
chmod +r flag.txt
cat flag.txt

su mission12
Password:
```

<figure><img src="../../.gitbook/assets/image (576).png" alt=""><figcaption></figcaption></figure>

















