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
nmap -sV -sT -O -p 1-65535 $VICTIM
```





## Vulnerability Discovery

After we've gained a basic familiarity with the site, we can start some more active enumeration. A directory brute force scan can help us discover content that's otherwise unreachable through normal browsing, like admin pages or old versions that haven't been removed. With admin pages leading to more application features that might not be hardened, or unmaintained code from outdated versions, we're more likely to find vulnerabilities.

Run a gobuster scan with dirb's `big.txt` wordlist. What can you find? Other tools and wordlists are available.

With an API driven webapp like this, we can see how the application retrieves data to make it dynamic. A great way to do this is Burp Suite, which can automatically map out the target site's structure as we explore.

## Discovering the overly verbose API

Burp Suite's target site map should have discovered 2 API routes that the website uses to retrieve information about the dog pictures. One retrieves the title and caption, and the other is used to retrieve the date and author. The full paths have been redacted, so that you find them yourself.

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5d96207dd832c106398e2267/room-content/03100fff977a5fb2c6c2c4cc3b82a241.png)\


It appears the API route used to retrieve the date and author does so with EXIF data, and uses Exiftool from the response. It appears the output of the command is simply serialised into JSON and sent to the client. This gives us a lot of information, notably the ExifTool version number. This is considered a vulnerability under the[ OWASP API Top 10](https://owasp.org/www-project-api-security/), more specifically `API3:2019 Excessive Data Exposure`. Usually, exposing version numbers would be considered a low or informational rated issue but as we will discover it can have more serious consequences.

### Answer the questions



