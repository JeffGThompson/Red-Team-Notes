# Windows Network Analysis

**Room Link:** [https://tryhackme.com/r/room/windowsnetworkanalysis](https://tryhackme.com/r/room/windowsnetworkanalysis)

## Windows Network Analysis

### Viewing Named-Pipes

Named-pipes are a method used by the Operating System to perform inter-process communication. Named-pipes can be either local or network-based. This task will show you how to list network-based Named-pipes, which can indicate processes communicating with another host (I.e. a file share or file upload).

### System Resource Usage Monitor (SRUM)

The SRUM is a Windows feature that tracks the last 30 to 60 days of resource usage, such as:

* Application and service activity
* Network activity, such as packets sent and received
* User activity (I.e. launching services or processes).

In a database (SRUDB.dat) on the host, this can be found at `C:\Windows\System32\sru\SRUDB.dat`.\


<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/5de96d9ca744773ea7ef8c00/room-content/506b48c9e52bf3b1b37ff7b6ab7d57e8.png" alt=""><figcaption></figcaption></figure>

However, we will need to use external tooling to process this database as there is no built-in utility that can be used to gather the information that we need. It should be noted that, during a live acquisition, this file is locked by Windows, so we will need to export it to our own machine. We can use utilities such as FTK Imager or KAPE to retrieve this.

**Using KAPE's SRUMDump module**

```shell-session
C:\Users\CMNatic\Desktop\kape>.\kape.exe --tsource C:\Windows\System32\sru --tdest C:\Users\CMNatic\Desktop\SRUM --tflush --mdest C:\Users\CMNatic\Desktop\MODULE --mflush --module SRUMDump --target SRUM
KAPE version 1.3.0.2, Author: Eric Zimmerman, Contact: https://www.kroll.com/kape (kape@kroll.com)


Creating module destination directory C:\Users\CMNatic\Desktop\MODULE
  Found processor Executable: srum_dump_csv.exe, Cmd line: -i %sourceDirectory%\WINDOWS\System32\sru\SRUDB.dat -t SRUM_TEMPLATE.xlsx -r %sourceDirectory%\WINDOWS\System32\config\SOFTWARE -o %destinationDirectory% -q, Export: csv, Append: False!

Total execution time: 13.3242 seconds
```

\


Once we have retrieved the SRUDB.dat file, we can use the [srum-dump](https://github.com/MarkBaggett/srum-dump) utility to analyse this database. After downloading the srum-dump executable and SRUM template from the repo, launch the executable and fill out the pop-up with the relevant information:

* Path to the exported SRUMDB.dat on our other analyst machine
* Path to the srum-dump template
* Path to output the srum-dump analysis file
* We can leave the registry boxes blank for now.\


<figure><img src="https://assets.tryhackme.com/additional/windowsnetworkanalysis/srum1.png" alt=""><figcaption></figcaption></figure>

And now let `srum-dump` analyse. This may take a couple of minutes. Feel free to grab a glass of water and stretch your legs.

![A terminal screenshot of SRUM Dump analysing the image, note a progress bar and the amount of rows that will be present in the output](https://assets.tryhackme.com/additional/windowsnetworkanalysis/srum2.png)

After a few minutes, the analysis will be complete. We can go to our output directory and open the Excel file.

![Viewing the SRUM\_DUMP output file in Windows Explorer />\<br />\</p>\<p>We will be presented with all of the information from the SRUMDB.dat. Located in tabs in the spreadsheet are the following useful types of information:\</p>\<ul>\<li>App Timeline Provider\</li>\<li>Network Data Usage\</li>\<li>Application Resource Usage\</li>\</ul>\<p>\<img src=](https://tryhackme-images.s3.amazonaws.com/user-uploads/5de96d9ca744773ea7ef8c00/room-content/6e365268e1b9dba32bb714e299bb5304.png)

Please note that this process has already been done for you on the practical VM.\


### Windows Firewall Logs

Before proceeding, check if logging is enabled. By default, Windows Firewall will log to `C:\Windows\System32\LogFiles\Firewall`.

<figure><img src="https://help.pdq.com/hc/article_attachments/4414447260955/WindowsFirewall_01.png" alt=""><figcaption></figcaption></figure>



<figure><img src="https://help.pdq.com/hc/article_attachments/4414435567131/WindowsFirewall_02.png" alt=""><figcaption></figcaption></figure>

After a few minutes, we can open the `pfirewall.log` located in `C:\Windows\System32\LogFiles\Firewall`. Here, we can see connections that Windows Firewall has allowed or dropped.

<figure><img src="https://assets.tryhackme.com/additional/windowsnetworkanalysis/firewall1.png" alt=""><figcaption></figcaption></figure>

You can, of course, view this using the `gc` (Get-Content) cmdlet in PowerShell:

**Using the Get-Content module to output the Firewall log**

```shell-powershell
PS C:\Users\Administrator\Desktop> gc C:\Windows\System32\LogFiles\Firewall\pfirewall.log | more
#Version: 1.5
#Software: Microsoft Windows Firewall
#Time Format: Local
#Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path

2024-03-19 11:24:43 ALLOW UDP 192.168.0.202 192.168.0.255 137 137 0 - - - - - - - RECEIVE
2024-03-19 11:24:47 ALLOW UDP 192.168.0.231 239.255.255.250 63913 1900 0 - - - - - - - SEND
2024-03-19 11:24:47 ALLOW 2 192.168.1.1 224.0.0.1 - - 0 - - - - - - - RECEIVE
2024-03-19 11:24:47 ALLOW 2 192.168.0.231 224.0.0.22 - - 0 - - - - - - - SEND
2024-03-19 11:24:48 ALLOW UDP 192.168.0.231 194.168.4.100 55048 53 0 - - - - - - - SEND
2024-03-19 11:24:48 ALLOW UDP 192.168.0.231 194.168.4.100 49369 53 0 - - - - - - - SEND
2024-03-19 11:24:50 ALLOW TCP 192.168.0.231 192.168.0.250 49736 80 0 - 0 0 0 - - - SEND
2024-03-19 11:24:50 ALLOW TCP 192.168.0.231 192.168.0.250 49737 80 0 - 0 0 0 - - - SEND
2024-03-19 11:24:51 ALLOW TCP 192.168.0.202 192.168.0.231 58427 3389 0 - 0 0 0 - - - RECEIVE
2024-03-19 11:24:53 ALLOW TCP 192.168.0.202 192.168.0.231 58428 3389 0 - 0 0 0 - - - RECEIVE
2024-03-19 11:24:53 ALLOW UDP 192.168.0.202 192.168.0.231 63842 3389 0 - - - - - - - RECEIVE
2024-03-19 11:24:55 ALLOW UDP 192.168.0.231 194.168.4.100 54343 53 0 - - - - - - - SEND
```

### Answer the questions&#x20;

**What is the full name of the Windows feature that tracks the last 30 to 60 days of system statistics?**

System Resource Usage Monitor (SRUM)

**What is the full path to the directory that Windows will output Firewall logs to?**

C:\Windows\System32\LogFiles\Firewall\\

## Network Analysis via PowerShell

PowerShell is an extremely powerful and extensive command shell for Windows with its own scripting language. It can be used to automate tasks, audit and configure the Windows operating system, and it is already provided on the machine.

We can use PowerShell to retrieve a lot of the same information that other tools can. Knowing how to retrieve network activity using PowerShell is a great "first step" in triaging a machine, especially when you can't immediately throw your toolset at it. This task is going to show you some example commands for PowerShell.

### Show TCP Connections and Associated Processes

This snippet can be a nice "quick win" to see what processes are making TCP connections and the IP addresses, where you can quickly find anomalies for further investigation.

\
**Using the Get-NetTCPConnetion cmdlet**

```shell-session
PS C:\Users\Administrator Get-NetTCPConnection | select LocalAddress,localport,remoteaddress,remoteport,state,@{name="process";Expression={(get-process -id $_.OwningProcess).ProcessName}}, @{Name="cmdline";Expression={(Get-WmiObject Win32_Process -filter "ProcessId = $($_.OwningProcess)").commandline}} | sort Remoteaddress -Descending | ft -wrap -autosize

192.168.0.101     50109 23.208.241.199        443 Established OUTLOOK     "C:\Program Files\Microsoft Office\root\Office16\OUTLOOK.EXE"
192.168.0.101     50131 51.15.43.212          443 Established msedge      "C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe"
192.168.0.101     50132 51.15.43.212          443 Established msedge      "C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe"
```



### Show UDP Connections

The following snippet will display all UDP connections. Whilst the majority of applications will be using TCP, viewing the UDP connections are helpful in building a picture of the activity of the machine. Additionally, hosts who are infected with joining a botnet, may use UDP to communicate, such as in the case of flood attacks.

\
**Using the Get-NetUDPEndpoint cmdlet**

```shell-powershell
PS C:\Users\Administrator Get-NetUDPEndpoint | select local*,creationtime, remote* | ft -autosize

LocalAddress  LocalPort creationtime        remote*
------------  --------- ------------        -------
::                64698 21/02/2024 11:54:02
::                 5355 21/02/2024 11:54:01
::                 5353 21/02/2024 11:54:01

0.0.0.0           64697 21/02/2024 11:54:02
127.0.0.1         58110 21/02/2024 11:54:02
0.0.0.0            5355 21/02/2024 11:54:01
0.0.0.0            5353 21/02/2024 11:54:01
0.0.0.0            4500 21/02/2024 11:54:01
0.0.0.0            3702 21/02/2024 11:54:02
0.0.0.0            3389 21/02/2024 11:54:01
0.0.0.0             500 21/02/2024 11:54:01
192.168.0.101       138 21/02/2024 11:54:01
192.168.0.17       3389 21/02/2024 10:26:39
192.168.0.200      4444 21/02/2024 09:32:05
0.0.0.0             123 21/02/2024 11:54:02
```



### Sort and Unique Remote IPs

This snippet can be used to list the IP addresses associated with ongoing TCP connections on the host, where they are then sorted in numerical order and uniqued (removing duplicates). The `-Unique` filter on `Sort-Object` is important because IP addresses can make multiple connections (I.e. a browser). The output from this snippet can be exported, where the IP addresses can be compared to threat intelligence or any possible events from security applications such as an IDS.

**Using the Get-NetTCPConnection cmdlet with a remoteaddress filter**

```shell-session
PS C:\Users\Administrator (Get-NetTCPConnection).remoteaddress | Sort-Object -Unique
::
::1
0.0.0.0
127.0.0.1
13.107.21.200
172.166.86.133
192.168.0.111
192.168.0.202
192.168.0.236
20.90.156.32
204.79.197.239
213.105.19.97
23.209.73.91
23.48.165.20
51.15.43.212
52.109.32.97
52.111.227.11
52.182.143.209
62.253.3.145
```

### Investigate an IP Address

If we wish to drill down into an IP address, we can use the following snippet to gain some more insight. For example, in the snippet below, we can see the connection status, the date and time it was initiated, the local port (local host) and a remote port (remote host), and the process causing that connection. This could be an excellent way to discover connections that an IP is making to a process. For example, a process from _payload.exe_ connecting to port 4444 on an IP address.

\
**Specifying an IP address to Get-NetTCPConnection**

```shell-session
PS C:\Users\Administrator Get-NetTCPConnection -remoteaddress 51.15.43.212 | select state, creationtime, localport,remoteport | ft -autosize

    State creationtime        localport remoteport
    ----- ------------        --------- ----------
CloseWait 21/02/2024 13:43:36     50151        443
CloseWait 21/02/2024 13:43:36     50150        443
```

### Retrieve DNS Cache

The DNS cache on a host is a locally stored "database" of DNS records and their corresponding host stored on the host, used to remember what domains match up to what IP address, improving performance (i.e, the host already knows where the domain points to). Reviewing the cache can be used to indicate what domains have recently been contacted.

\
**Using the Get-DnsClientCache cmdlet**

```shell-powershell
PS C:\Users\Administrator Get-DnsClientCache | ? Entry -NotMatch "workst|servst|memes|kerb|ws|ocsp" | out-string -width 1000

attacker.thm    AAAA   NoRecords                                                                                                                                                             
attacker.thm    A      Success   Answer  598005      4 10.10.182.37
```

### View Hosts File

The hosts file contains "override" domains and the associated IP address on the host. You will be familiar with this concept if you have completed challenges on TryHackMe. It is a useful method of mapping a domain to an IP without relying on a DNS server.

Attackers use the hosts file to redirect traffic to something they control, as the host will take preference from the hosts file before reaching out to a DNS server. For example, an attacker may be able to tell the host to send all traffic destined to [http://exampledomain.com](http://exampledomain.com/)  (which is owned by the business) to the attacker's servers instead...whilst all the user will see is [http://exampledomain.com](http://exampledomain.com/).

There are numerous examples of this being used in banking trojans, phishing attacks, etc, where the user thinks they're logging into the correct page because the URL matches up. Meanwhilst, the traffic is being sent to the attacker's servers instead.

\
**Using gc to output the last four lines of the hosts file**

```shell-powershell
PS C:\Users\Administrator gc -tail 4 "C:\Windows\System32\Drivers\etc\hosts"
#       ::1             localhost
192.168.0.200 attacker.thm
```

### Querying WinRM Sessions

Windows Remote Management (WinRM) administers a system over the command line. This is useful for automated scripts (and the such) by the network administrators. However, Attackers can abuse this same functionality in a stealthier way than connecting to a device over, say, RDP, to execute PowerShell commands remotely.

Querying WindowsRM sessions is essential, especially as sessions can persist (i.e. backdoors).

\


### Querying RDP Logs

Querying active and recent RDP connections is another "quick win" to understand the current activity on the host. The qwinsta command will show the user status, as well as source of the connection.

**Using qwinsta to display remote desktop sessions**

```shell-powershell
PS C:\Users\Administrator qwinsta
 SESSIONNAME       USERNAME                 ID  STATE   TYPE        DEVICE
 services                                    0  Disc
 console                                     1  Conn
 rdp-tcp#2         Attacker             2  Active
 rdp-tcp                                 65536  Listen
PS C:\Users\Administrator
```

### Querying SMB Shares

**Using the Get-SmbConnection to display established SMB Connections**

```shell-powershell
PS C:\Users\Administrator Get-SmbConnection

ServerName    ShareName UserName             Credential           Dialect NumOpens
----------    --------- --------             ----------           ------- --------
10.10.124.230 c         THM-Analyst\Administrator THM-Analyst\Administrator 3.1.1   5
10.10.124.230 c$        THM-Analyst\Administrator THM-Analyst\Administrator 3.1.1   1
10.10.124.230 IPC$      THM-Analyst\Administrator THM-Analyst\Administrator 3.1.1   0
```

### Answer the questions&#x20;

**What cmdlet can be used to display active TCP connections?**

Get-NetTCPConnection

**What cmdlet can be used to display the DNS cache on the host?**\
Get-DnsClientCache

**What command can be used to list all active RDP sessions on the host?**\
qwinsta

## Internal Tooling

### Packet Monitor (Pktmon)

[Packet Monitor](https://learn.microsoft.com/en-us/windows-server/networking/technologies/pktmon/pktmon) is a Microsoft-developed packet sniffing tool provided with Windows 10, Windows Server 2022 and Server 2019 that works on the network stack. A "cheatsheet" of the commands has been provided in the table below:

| Command           | Description                                                                  |
| ----------------- | ---------------------------------------------------------------------------- |
| `pktmon start`    | Start a PacketMonitor capture.                                               |
| `pktmon stop`     | Stop a PacketMonitor capture.                                                |
| `pktmon reset`    | Reset the count of packets that PacketMonitor has captured.                  |
| `pktmon counters` | View the amount of packets PacketMonitor has captured across the interfaces. |
| `pktmon etl2txt`  | Convert a PacketMonitor capture file to a text file.                         |
| `pktmon etl2pcap` | Convert a PacketMonitor capture file to a pcap.                              |

### Starting a PacketMonitor capture

```shell-session
C:\Windows\system32>pktmon start -c

Logger Parameters:
    Logger name:        PktMon
    Logging mode:       Circular
    Log file:           C:\Windows\system32\PktMon.etl
    Max file size:      512 MB
    Memory used:        768 MB

Collected Data:
    Packet counters, packet capture

Capture Type:
    All packets

Monitored Components:
    All

Packet Filters:
    None
```

### Netstat

[Netstat](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/netstat) is another Microsoft-developed utility that can be used to review TCP/UDP connections on the machine. Similar to Packet Monitor, this utility is already provided on Windows.

To begin, we can launch a new cmd and enter `netstat -a`. This will instruct Netstat to list all active connections.

**Using Netstat to capture network traffic on all interfaces**

```shell-powershell
C:\Users\tryhackme:~$ netstat -a
  TCP    127.0.0.1:143          mail:49725             ESTABLISHED
  TCP    127.0.0.1:49725        mail:imap              ESTABLISHED
  TCP    192.168.0.101:139      thm-monikerlink:0      LISTENING
  TCP    192.168.0.101:3389     DESKTOP-IS0280F:50792  ESTABLISHED
  TCP    192.168.0.101:49682    20.90.156.32:https     ESTABLISHED
  TCP    192.168.0.101:49723    52.123.242.73:https    TIME_WAIT
  TCP    192.168.0.101:49728    192.229.221.95:http    TIME_WAIT
  TCP    192.168.0.101:49738    a23-37-2-8:https       ESTABLISHED
  TCP    192.168.0.101:49740    a23-37-1-150:http      TIME_WAIT
```

However, as you'll come to notice, this generates a lot of noise. Netstat can be configured to dive deeper into some of these connections as well as filter out some of this noise. A cheatsheet of some options has been provided in the table below.

| Command      | Description                                                                               |
| ------------ | ----------------------------------------------------------------------------------------- |
| `netstat -a` | Display all currently active TCP connections and TCP/UDP ports.                           |
| `netstat -b` | Display the executable responsible for the connection (i.e. payload.exe).                 |
| `netstat -o` | Display all TCP connections and include the process ID.                                   |
| `netstat -p` | Display connections by protocol. Options include TCP, UDP, ICMP, and the IPV6 iterations. |

It is worth noting that these options can be combined together. An example of this is in the snippet below, where `-a` and `-b` are used to show active TCP connections and the executable responsible for them.

**Using Netstat to capture network traffic on all interfaces**

```shell-powershell
C:\Users\tryhackme>netstat -a -b

Active Connections

  Proto  Local Address          Foreign Address        State
  TCP    0.0.0.0:135            thm-c2:0               LISTENING
  RpcSs
 [svchost.exe]
  TCP    0.0.0.0:445            thm-c2:0               LISTENING
 Can not obtain ownership information
  TCP    0.0.0.0:3389           thm-c2:0               LISTENING
  TermService
 [svchost.exe]
  TCP    0.0.0.0:5985           thm-c2:0               LISTENING
 Can not obtain ownership information
  TCP    0.0.0.0:47001          thm-c2:0               LISTENING
 Can not obtain ownership information
  TCP    0.0.0.0:49664          thm-c2:0               LISTENING
 Can not obtain ownership information
  TCP    0.0.0.0:49665          thm-c2:0               LISTENING
  EventLog
 [svchost.exe]
  TCP    0.0.0.0:49666          thm-c2:0               LISTENING
```

### Viewing Network Connections With PID

**Using Netstat to show active connections and associatedPID**

```shell-powershell
C:\Users\tryhackme~$ netstat -a -o
Active Connections

  Proto  Local Address          Foreign Address        State           PID
  TCP    0.0.0.0:135            thm-c2:0      LISTENING       1608
  TCP    0.0.0.0:445            thm-c2:0      LISTENING       4
  TCP    0.0.0.0:903            thm-c2:0      LISTENING       6052
  TCP    0.0.0.0:913            thm-c2:0      LISTENING       6052
  TCP    0.0.0.0:2869           thm-c2:0      LISTENING       4
  TCP    0.0.0.0:5040           thm-c2:0      LISTENING       8656
  TCP    0.0.0.0:5357           thm-c2:0      LISTENING       4
  TCP    0.0.0.0:7070           thm-c2:0      LISTENING       5856
  TCP    0.0.0.0:7396           thm-c2:0      LISTENING       23368
  TCP    0.0.0.0:7680           thm-c2:0      LISTENING       56648
  TCP    0.0.0.0:9993           thm-c2:0      LISTENING       6120
```

We can then use resources such as Task Manager or `pmon` to look for the process' PID.

<figure><img src="https://assets.tryhackme.com/additional/windowsnetworkanalysis/netstat1.png" alt=""><figcaption></figcaption></figure>

### Exporting Netstat

The output of Netstat can also be exported by using a redirection (`C:\Users\tryhackme:~$ netstat -a -o > netstat.txt`). This is extremely helpful when we export or search the logs.

<figure><img src="https://assets.tryhackme.com/additional/windowsnetworkanalysis/netstat2.png" alt=""><figcaption></figcaption></figure>

### Answer the questions

**What netstat flag can we use to display the executable responsible for a connection?**

\-b

**If we wanted to display all TCP connections and the associated process ID using netstat, what flag would we use?**

\-o

**What special character can we use to save the output of netstat to a text file?**

\>

## Practical

### Answer the questions

**Use the Get-NetTCPConnection PowerShell cmdlet to list the connections currently active.**

**A popular port for reverse shells is currently active. What is the port number?**

**If nothing sticks out, wait a few minutes and run the command again.**

**Victim**

```
Get-NetTCPConnection | select LocalAddress,localport,remoteaddress,remoteport,state,@{name="process";Expression={(get-process -id $_.OwningProcess).ProcessName}}, @{Name="cmdline";Expression={(Get-WmiObject Win32_Process -filter "ProcessId = $($_.OwningProcess)").commandline}} | sort Remoteaddress -Descending | ft -wrap -autosize
```

<figure><img src="../../.gitbook/assets/image (1182).png" alt=""><figcaption></figcaption></figure>



**What is the name of the process that is connecting to the C2 server?**

**Victim**

```
Get-NetTCPConnection | select LocalAddress,localport,remoteaddress,remoteport,state,@{name="process";Expression={(get-process -id $_.OwningProcess).ProcessName}}, @{Name="cmdline";Expression={(Get-WmiObject Win32_Process -filter "ProcessId = $($_.OwningProcess)").commandline}} | sort Remoteaddress -Descending | ft -wrap -autosize
```

<figure><img src="../../.gitbook/assets/image (1183).png" alt=""><figcaption></figcaption></figure>



**What is the domain that has been added to the workstation's host file?**

**Victim**

```
gc -tail 4 "C:\Windows\System32\Drivers\etc\hosts"
```

<figure><img src="../../.gitbook/assets/image (1184).png" alt=""><figcaption></figcaption></figure>

**Analyse the SRUM database. There is another process that has sent a large amount of bytes, indicating data exfil. What is the full path to the process (as listed in SRUM)?**

\device\harddiskvolume3\program files\updater\exfil.exe

<figure><img src="../../.gitbook/assets/image (1185).png" alt=""><figcaption></figcaption></figure>

**Finally, analyse the SMB shares present on the analyst machine. What is the name of the share that stands out?**

**Victim**

```
Get-SMBShare
```

<figure><img src="../../.gitbook/assets/image (1186).png" alt=""><figcaption></figcaption></figure>
