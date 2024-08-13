# XSS

**Room Link:** [https://tryhackme.com/r/room/axss](https://tryhackme.com/r/room/axss)

## Terminology and Types

As already stated, XSS is a vulnerability that allows an attacker to inject malicious scripts into a web page viewed by another user. Consequently, they bypass the **Same-Origin Policy (SOP)**; SOP is a security mechanism implemented in modern web browsers to prevent a malicious script on one web page from obtaining access to sensitive data on another page. SOP defines origin based on the protocol, hostname, and port. Consequently, a malicious ad cannot access data or manipulate the page or its functionality on another origin, such as an online shop or bank page. XSS dodges SOP as it is executing from the same origin.

### JavaScript for XSS

Basic knowledge of JavaScript is pivotal for understanding XSS exploits and adapting them to your needs. Knowing that XSS is a client-side attack that takes place on the target’s web browser, we should try our attacks on a browser similar to that of the target. It is worth noting that different browsers process certain code snippets differently. In other words, one exploit code might work against Google Chrome but not against Mozilla Firefox or Safari.

Suppose you want to experiment with some JavaScript code in your browser. In that case, you need to open the Console found under Web Developer Tools on Firefox, Developer Tools on Google Chrome, and Web Inspector on Safari. Alternatively, use the respective shortcuts:

* On Firefox, press Ctrl + Shift + K
* On Google Chrome, press Ctrl + Shift + J
* On Safari, press Command + Option + J

![A web browser with the Console tab ready.](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/ea22f6ec937fa5d9c24dc024372e357b.png)\


Let’s review and try some essential JavaScript functions:

* **Alert**: You can use the `alert()` function to display a JavaScript alert in a web browser. Try `alert(1)` or `alert('XSS')` (or `alert("XSS")`) to display an alert box with the number 1 or the text XSS.
* **Console log**: Similarly, you can display a value in the browser’s JavaScript console using `console.log()`. Try the following two examples in the console log: `console.log(1)` and `console.log("test text")` to display a number or a text string.
* **Encoding**: The JavaScript function `btoa("string")` encodes a string of binary data to create a base64-encoded ASCII string. This is useful to remove white space and special characters or encode other alphabets. The reverse function is `atob("base64_string")`.

Furthermore, you can experiment with displaying values, such as the `document.cookie` by using `alert(document.cookie)` for example.

![A web browser with an alert displaying Hello World!](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/0ba756c3c7a74a2cc0fecdca6311b117.png)\


![A web browser with the Console tab showing example JavaScript functions.](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/35bc1c17a4e32135c8a75593c0fbba7f.png)\


### Types of XSS

To recap from the [Intro to Cross-site Scripting](https://tryhackme.com/room/xss) room, there are three main types of XSS:

* **Reflected XSS**: This attack relies on the user-controlled input reflected to the user. For instance, if you search for a particular term and the resulting page displays the term you searched for (_**reflected**_), the attacker would try to embed a malicious script within the search term.
* **Stored XSS**: This attack relies on the user input stored in the website’s database. For example, if users can write product reviews that are saved in a database (_**stored**_) and being displayed to other users, the attacker would try to insert a malicious script in their review so that it gets executed in the browsers of other users.
* **DOM-based XSS**: This attack exploits vulnerabilities within the Document Object Model (**DOM**) to manipulate existing page elements without needing to be reflected or stored on the server. This vulnerability is the least common among the three.

## Causes and Implications

Cross-site scripting (XSS) is a web security vulnerability that allows an attacker to inject malicious scripts into a web page viewed by other users. As a result, the unsuspecting users end up running the unauthorized script in their browsers, although the website they are visiting is trusted to be benign. Therefore, XSS can be a severe threat because it exploits users’ trust in a site.

### What Makes XSS Possible

There are many reasons why XSS vulnerabilities are still found in web apps. Below, we list a few of them.

**Insufficient input validation and sanitization**

Web applications accept user data, e.g., via forms, and use this data in the dynamic generation of HTML pages. Consequently, malicious scripts can be embedded as part of the legitimate input and will eventually be executed by the browser unless adequately sanitized.

**Lack of output encoding**

The user can use various characters to alter how a web browser processes and displays a web page. For the HTML part, it is critical to properly encode characters such as `<`, `>`, `"`, `'`, and `&` into their respective HTML encoding. For JavaScript, special attention should be given to escape `'`, `"`, and `\`. Failing to encode user-supplied data correctly is a leading cause of XSS vulnerabilities.

**Improper use of security headers**

Various security headers can help mitigate XSS vulnerabilities. For example, Content Security Policy (CSP) mitigates XSS risks by defining which sources are trusted for executable scripts. A misconfigured CSP, such as overly permissive policies or the improper use of `unsafe-inline` or `unsafe-eval` directives, can make it easier for the attacker to execute their XSS payloads.

**Framework and language vulnerabilities**

Some older web frameworks did not provide security mechanisms against XSS; others have unpatched XSS vulnerabilities. Modern web frameworks automatically escape XSS by design and promptly patch any discovered vulnerability.

**Third-party libraries**

Integrating third-party libraries in a web application can introduce XSS vulnerabilities; even if the core web application is not vulnerable.

![A malicious hacker writes a website comment that starts with a greeting and expresses that this is their first post. Furthermore, they include a URL that looks like it steals the user's cookie.](https://tryhackme-images.s3.amazonaws.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/984673339466701119cb694803ff0d27.svg)\


### Implications of XSS

There are many implications of XSS. Below, we list a few of them.

**Session hijacking**

As XSS can be used to steal session cookies, attackers can take over the session and impersonate the victim if successful.

**Phishing and credential theft**

Leveraging XSS, attackers can present a fake login prompt to the user. In one recent case, the browser’s page was partially hidden by a dialogue box requesting users to connect to their cryptocurrency wallet.

**Social engineering**

Using XSS, an attacker can create a legitimate-looking pop-up or alert within a trusted website. This can trick users into clicking malicious links or visiting malicious websites.

**Content manipulation and defacement**

In addition to phishing and social engineering, an attacker might use XSS to change the website for other purposes, such as inflicting damage on the company’s reputation.

**Data exfiltration**

XSS can access and exfiltrate any information displayed on the user’s browser. This includes sensitive information such as personal data and financial information.

**Malware installation**

A sophisticated attacker can use XSS to spread malware. In particular, it can deliver drive-by download attacks on the vulnerable website.

## CVE-2023-38501 - Vulnerable Web Application 1&#x20;

The attached VM runs a vulnerable version of [copyparty](https://github.com/9001/copyparty). The discovered reflected XSS vulnerability has the ID [CVE-2023-38501](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2023-38501), and its exploit is published [here](https://www.exploit-db.com/exploits/51635).

The exploit code is `?k304=y%0D%0A%0D%0A%3Cimg+src%3Dcopyparty+onerror%3Dalert(1)%3E` which is the URL encoding of:

```
?k304=y <img src=copyparty onerror=alert(1)>
```

The attached VM has the vulnerable server running at port 3923. You can reach the vulnerable server at `http://10.10.225.118:3923` via your AttackBox’s browser.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>













