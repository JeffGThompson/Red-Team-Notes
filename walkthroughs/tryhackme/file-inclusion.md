# File Inclusion

**Room Link:** [https://tryhackme.com/room/fileinc](https://tryhackme.com/room/fileinc)



**Give Lab #1 a try to read /etc/passwd. What would the request URI be?**

<figure><img src="../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

**In Lab #2, what is the directory specified in the include function?**

<figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

**Give Lab #3 a try to read /etc/passwd. What is the request look like?**

Input field didn't work but we were able to bypass by entering our command in the web browser instead.

**URL:** hxxp://10.10.230.14/lab3.php?file=..//..//../../../etc/passwd%00&#x20;

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>



