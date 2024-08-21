# CSRF

**Room Link:** [https://tryhackme.com/r/room/csrfV2](https://tryhackme.com/r/room/csrfV2)

## Overview of CSRF Attack

What is CSRF?

CSRF is a type of security vulnerability where an attacker tricks a user's web browser into performing an unwanted action on a trusted site where the user is authenticated. This is achieved by exploiting the fact that the browser includes any relevant cookies (credentials) automatically, allowing the attacker to forge and submit unauthorised requests on behalf of the user (through the browser). The attacker's website may contain HTML forms or JavaScript code that is intended to send queries to the targeted web application.

Cycle of CSRF

A CSRF attack has three essential phases:

* The attacker already knows the format of the web application's requests to carry out a particular task and sends a malicious link to the user.
* The victim's identity on the website is verified, typically by cookies transmitted automatically with each domain request and clicks on the link shared by the attacker. This interaction could be a click, mouse over, or any other action.

![phases of csrf](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/61e098019fb1d8611c0aa16f23c34633.svg)

* Insufficient security measures prevent the web application from distinguishing between authentic user requests and those that have been falsified.

Effects of CSRF

Understanding CSRF's impact is crucial for keeping online activities secure. Although CSRF attacks don't directly expose user data, they can still cause harm by changing passwords and email addresses or making financial transactions. The risks associated with CSRF include:

* Unauthorised Access: Attackers can access and control a user's actions, putting them at risk of losing money, damaging their reputation, and facing legal consequences.
* Exploiting Trust: CSRF exploits the trust websites put in their users, undermining the sense of security in online browsing.
* Stealthy Exploitation: CSRF works quietly, using standard browser behaviour without needing advanced malware. Users might be unaware of the attack, making them susceptible to repeated exploitation.

Understanding these risks is essential for implementing effective measures to protect web applications from CSRF vulnerabilities.

## Types of CSRF Attack

Traditional CSRF

Conventional CSRF attacks frequently concentrate on state-changing actions carried out by submitting forms. The victim is tricked into submitting a form without realising the associated data like cookies, URL parameters, etc. The victim's web browser sends an HTTP request to a web application form where the victim has already been authenticated. These forms are made to transfer money, modify account information, or alter an email address.

![traditional csrf](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/ef1cd0a1d90c6fbdaeed3b0a29987e01.svg)\


The above diagram shows traditional CSRF examples in the following steps:

* The victim is already logged on to his bank website. The attackers create a crafted malicious link and email it to the victim.
* The victim opens the email in the same browser.
* Once clicked, the malicious link enables the auto-transfer of the amount from the victim's browser to the attacker's bank account.

XMLHttpRequest CSRF

An asynchronous CSRF exploitation occurs when operations are initiated without a complete page request-response cycle. This is typical of contemporary online apps that leverage asynchronous server communication (via XMLHttpRequest or the Fetch API) and JavaScript to produce more dynamic user interfaces. These attacks use asynchronous calls instead of the more conventional form submissions. Still, they exploit the same trust relationship between the user and the online service.\


Consider an online email client, for instance, where users may change their email preferences without reloading the page. If this online application is CSRF-vulnerable, a hacker might create a fake asynchronous HTTP request, usually a POST request, and alter the victim's email preferences, forwarding all their correspondence to a malicious address.

The following is a simplified overview of the steps that an asynchronous CSRF attack could take:&#x20;

* The victim opens a session saved in their browser's cookies and logs into the `mailbox.thm`.\

* The attacker entices the victim to open a malicious webpage with a script that can send queries to the `mailbox.thm`.\

* To modify the user's email forwarding preferences, the malicious script on the attacker's page makes an AJAX call to `mailbox.thm/api/updateEmail` (using XMLHttpRequest or Fetch).
* The `mailbox.thm` session cookie is included with the AJAX request in the victim's browser.
* After receiving the AJAX request, mailbox.thm evaluates it and modifies the victim's settings if no CSRF defences exist.

Flash-based CSRF

The term "Flash-based CSRF" describes the technique of conducting a CSRF attack by taking advantage of flaws in Adobe Flash Player components. Internet applications with features like interactive content, video streaming, and intricate animations![flash based csrf](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/328966e16f936388539965b9bf110581.svg) have been made possible with Flash. But over time, security flaws in Flash, particularly those that can be used to launch CSRF attacks, have become a major source of worry. As HTML5 technology advanced and security flaws multiplied, official support for Adobe Flash Player ceased on [December 31, 2020](https://www.adobe.com/products/flashplayer/end-of-life.html).

Even though Flash is no longer supported, a talk about Flash-based cross-site request forgery threats is instructive, particularly for legacy systems that still rely on antiquated technologies. A malicious Flash file (.swf) posted on the attacker's website would typically send unauthorised requests to other websites to carry out Flash-based CSRF attacks.



## Basic CSRF - Hidden Link/Image Exploitation

Overview

A covert technique known as hidden link/image exploitation in CSRF involves an attacker inserting a 0x0 pixel image or a link into a webpage that is nearly undetectable to the user. Typically, the `src or href` element of the image is set to a destination URL intended to act on the user's behalf without the user's awareness. It takes benefit of the fact that the user's browser transfers credentials like cookies automatically.\


```html
<!-- Website --> 
<a href="https://mybank.thm/transfer.php" target="_blank">Click Here</a>  
<!-- User visits attacker's website while authenticated -->
```

This technique preys on authenticated sessions and utilises a social engineering approach when a user may inadvertently perform operations on a different website while still logged in.![Josh with money briefcase](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/23d7c211fc5fd9f355d0d9710ab04e35.svg)

How it Works

Now, you are already connected to the VM. Let's see how the attack works.

* The attached VM represents Josh's machine, who uses Chrome to check his mailbox (`http://mailbox.thm:8081`) and log into his bank account (`http://mybank.thm:8080`). Since he is a financial broker by profession, he deals with many financial transactions daily and keeps his accounts logged into the browser. \

* The attacker somehow learns that Josh uses `mybank.thm:8080` for transactions, and his account is always logged in.\

* The attacker also had an account in the same bank, so he tried to log in and check for any vulnerabilities that he could use to get additional cash in his account.
* You can also log in to Firefox in the attached VM or any other browser using the username `GB82MYBANK5698` and password `GB82MYBANK5698` to understand the attacker's perspective and check the source code and how the client-side scripts work.
* While scanning, he found that no additional parameter was being sent from the bank app while transferring payment. Here is the code that handles the transfer of funds:

```php
<?php 
<form action="transfer.php" method="post">

    <label for="to_account">To Account:</label>
    <input type="text" id="to_account" name="to_account" required>

    <label for="amount">Amount:</label>
    <input type="number" id="amount" name="amount" required>

    <button type="submit">Transfer</button>
</form>
```

* Here, the attacker can employ the hidden image technique by simply crafting an email and luring the victim to click on a link. The attacker only knows Josh's email address, so he launches a social engineering attack by sending the following email to him.

![email screenshot](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/269742699563539c567fc258e484abf5.png)\


* The most important thing is the image src attribute set to the bank transfer page link. The attacker can hide it or put a valid image with an [anchor](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/a) tag redirecting to the bank transfer page.
* ```html
  <a href="http://mybank.thm:8080/dashboard.php?to_account=GB82MYBANK5698&amount=1000" target="_blank">Click Here to Redeem</a>
  ```
* To understand the victim's perspective, navigate to Josh's mailbox at the `mailbox.thm:8081` to see a new email stating that he has won a trip to Dubai and to claim it, he has to click on a link directed to the MyBank vulnerable page without having CSRF protection.
* Once you (in reality, the victim) click on the link, he will be redirected to the bank page, and the funds will be transferred automatically.

![successful transfer of funds](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/e3ca02b0792391277b2cc0be99a39128.png)\


What was the missing link?

All the validations are correct and accurate; however, the server is not validating if the request comes from a legitimate user.\


Securing the Breach

* From a pentester/red teamer perspective, it is vital to pentest each request and response parameter of the form.\

* From a secure coder perspective, it is important to ensure that each request submitted to the web server carries a unique token so that the server can identify if it is clicked through a valid source.
* The bank IT team quickly identified the issue and added a CSRF token with each request submitted to the server. The following code contains the updated client-side code. We can see that there is an additional hidden parameter, `csrf_token`, which will be sent to the server with each request.
* ```html
  <form method="post" action="">
          <label for="password">Password:</label>
          <input type="password" id="password" name="current_password" required>

          <label for="confirm_password">ConfirmPassword:</label>
          <input type="password" id="confirm_password" name="confirm_password" required>
  		<input type="hidden" id="csrf_token" name="csrf_token" value="<?php echo $_COOKIE['csrf-token']; ?>">
          <button type="submit" name="password_submit" >Update Password</button>
      </form>submit">
  </form> 
  ```
* On the server side, the server will verify if each incoming request contains the unique token; otherwise, it will reject the request.
* Open Josh's mailbox and navigate to the attacker's email with a green badge.\


<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/2ebbbeae74e609e80b917554c9da1a35.png" alt=""><figcaption></figcaption></figure>

* When the user clicks on the link, it won't transfer the funds as the request is missing a unique CSRF token required to validate the request.&#x20;

<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/357b5920d88617cd720647ecdfa760da.png" alt=""><figcaption></figcaption></figure>

## Double Submit Cookie Bypass

Overview

We've observed that without CSRF tokens, the bank web application was susceptible to vulnerabilities, exposing it to potential exploitation. However, the introduction of CSRF tokens significantly enhances security measures.

A CSRF token is a unique, unpredictable value associated with a user's session, ensuring each request comes from a legitimate source. One effective implementation is the [Double Submit Cookies technique](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site\_Request\_Forgery\_Prevention\_Cheat\_Sheet.html#alternative-using-a-double-submit-cookie-pattern), where a cookie value corresponds to a value in a hidden form field. When the server receives a request, it checks that the cookie value matches the form field value, providing an additional layer of verification.&#x20;

How it works

* Token Generation: When a user logs in or initiates a session, the server generates a unique CSRF token.![how double submit cookie attack works](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/63340a0ec9d725fcb640fbc3eedcbcf3.svg) This token is sent to the user's browser both as a cookie (CSRF-Token cookie) and embedded in hidden form fields of web forms where actions are performed (like money transfers).
* User Action: Suppose the user wants to transfer money. They fill out the transfer form on the website, which includes the hidden CSRF token.
* Form Submission: Upon submitting the form, two versions of the CSRF token are sent to the server: one in the cookie and the other as part of the form data.
* Server Validation: The server then checks if the CSRF token in the cookie matches the one sent in the form data. If they match, the request is considered legitimate and processed; if not, the request is rejected.\


Possible Vulnerable Scenarios

Despite its effectiveness, it's crucial to acknowledge that hackers are persistent and have identified various methods to bypass Double Submit Cookies:

* Session Cookie Hijacking (Man in the Middle Attack): If the CSRF token is not appropriately isolated and safeguarded from the session, an attacker may also be able to access it by other means (such as malware, network spying, etc.).
* Subverting the Same-Origin Policy (Attacker Controlled Subdomain): An attacker can set up a situation where the browser's same-origin policy is broken. Browser vulnerabilities or deceiving the user into sending a request through an attacker-controlled subdomain with permission to set cookies for its parent domain could be used.
* Exploiting XSS Vulnerabilities: An attacker may be able to obtain the CSRF token from the cookie or the page itself if the web application is susceptible to Cross-Site Scripting (XSS). By creating fraudulent requests with the double-submitted cookie CSRF token, the attacker can get around the defence once they have the CSRF token.
* Predicting or Interfering with Token Generation: An attacker may be able to guess or modify the CSRF token if the tokens are not generated securely and are predictable or if they can tamper with the token generation process.
* Subdomain Cookie Injection: Injecting cookies into a user's browser from a related subdomain is another potentially sophisticated technique that might be used. This could fool the server's CSRF protection system by appearing authentic to the main domain.

In this task, we will use one of the above techniques to bypass a variant of the Double Submit cookie technique.

How it Works

Now, we will see how the attack works, connecting it to the previous example. This technique will chain two vulnerable scenarios by reversing token generation and injecting a cookie through an attacker-controlled subdomain.&#x20;

* The attacker has successfully transferred the amount from Josh's bank account but seems more greedy and wants to take complete control of the bank account.
* To exploit the account, the attacker must access the bank account. But how? This requires the password of the Josh account.
* Exploiting a vulnerability like CSRF requires a lot of code and functionality and understanding the website logic from the developer's point of view. The attacker logged into his bank account and noticed that the form for updating passwords is protected through a CSRF- token.
* ```html
  <form method="post" action="">
          <label for="password">Password:</label>
          <input type="password" id="password" name="new_password" required>

          <label for="confirm_password">ConfirmPassword:</label>
          <input type="password" id="confirm_password" name="confirm_password" required>
  		<input type="hidden" id="csrf_token" name="csrf_token" value="<?php echo $_COOKIE['csrf-token']; ?>">
          <button type="submit" name="password_submit" >Update Password</button>
      </form>submit">
  </form> 
  ```
* But does that ultimately resolve the problem? An attacker can only bypass this security measure if he finds a way to reverse-engineer the token.
* He opens the Chrome built-in `Inspect element` console to verify the cookies created locally by the website.

![csrf-token cookie in chrome](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/ae41dfc7d2c56d13ab5461e23919addb.png)

_Click to enlarge the image._

* The attackers identified two relevant cookies created in the browser: `csrf-token` and `PHPSESSID`. Typically, the PHPSESSID is randomly generated by the PHP engine itself, and it is not very easy to reverse it for session hijacking. Consequently, the attacker copied his token and noticed if he could reverse engineer it.\

* He used [Cyber Chef](https://gchq.github.io/CyberChef/#recipe=From\_Base64\('A-Za-z0-9%2B/%3D',true,false\)\&input=UjBJNE1rMVpRa0ZPU3pVMk9UZ0s) to see if he could decode the string and, surprisingly, noticed that the CSRF token was a string he could decrypt.

![reversing the token through cyber chef](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/2726df6a335d01d4e41ba634ebdd896e.png)\


* He noticed that the string decoded from the token was his bank account number, meaning the web application developer needed to implement CSRF tokens better to avoid exploitation. We know that the attacker already has access to the subdomain of `mybank.thm` which can help him inject the cookie and has already reverse-engineered the cookie. Now, it's time to prepare a payload.\


Preparing Payload

* For the attack to work, the attacker would use the social engineering technique to make the victim click on a link.
* The attacker will prepare an enticing email to compel the user to change his password. He prepared an email citing a suspicious login attempt to Josh email and sent it to Josh.

![email screenshot](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/1ef37fe99417df64e42cb2c3afd73740.png)\


* The email contained a link to an attacker-controlled domain (`attacker.mybank.thm`) with a password update form similar to a bank one. The form also has a CSRF token already set as a hidden parameter.

```html
<form method="post" action="http://mybank.thm:8080//changepassword.php" id="autos">
        <label for="password">Password:</label>
        <input type="password" id="password" name="current_password" value="<?php echo "GB82MYBANK5697" ?>" required>

        <label for="confirm_password">ConfirmPassword:</label>
        <input type="password" id="confirm_password" name="confirm_password" value="Attacker Unique Password" required>
		<input type="hidden" id="csrf_token" name="csrf_token" value="Decrypted Token Value">
		

        <button type="submit" name="password_submit"  id="password_submit" >Update Password</button>
    </form>
	
	</div>
<script>
document.getElementById('password_submit').click(); 
</script>
        
```

* In the above code, the attacker will update the payload and add the `base64 value` of Josh's bank account as a new CSRF token value.&#x20;
* Navigate to Josh's mailbox at the `mailbox.thm:8081` to see the new mail with the crafted CSRF payload.

![selected email screenshot](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/32ef629045775c0616918e9caf0536a5.png)\


* Once you click the link, it will redirect you to the attacker-controlled subdomain page and auto-submit the form to change the password. The attacker contains a code similar to that of the change password page of `mybank.thm`; however, he is also setting a cookie with the following code so that it will be forwarded to the main domain `mybank.thm` while triggering a CSRF attack.\

* ```php
  <?php
  ...
  setcookie(
      'csrf-token',               
      base64_encode("GB82MYBANK5699"),            
      [
          'expires' => time() + (365 * 24 * 60 * 60), 
          'path' => '/',                         
          'domain' => 'mybank.thm',                          
          'secure' => false,                      
          'httponly' => false,                 
          'samesite' => 'Lax' 
      ]
  );
  ?>
  ```
* The attacker knows the hidden form parameter, and the form will submit all the data, including the hidden fields, to the original change password page of `mybank.thm:8080`. Following is the server-side code, let's see what is happening on the server:\

* ```php
  <?php
  if (base64_decode($_POST["csrf_token"]) == base64_decode($_COOKIE['csrf-token'])) { 
  // Retrieve form data
  $currentPassword = $_POST["current_password"];
  $newPassword = $_POST["confirm_password"];
  // Update Password
  ...;
  ```
* As we see in the above code, the server will decode the CSRF token and compare it with the cookie value. The server hosting `mybank.thm:8080` will consider the request legitimate as it comes from a logged-in user, and the CSRF token is also valid and will update the password.

![successful password update](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/367f1d6912e97add5d29a138b39af10b.png)\


Note: For ease, the server request headers are displayed on the webpage to understand received cookies and form data.

What was the missing link?

The server-side script correctly validated the user; however, the CSRF token was easy to predict, and therefore, the attacker could launch a crafted CSRF attack.\


Securing the Breach

* From a pentester/red teamer perspective, it is essential to validate the complete flow of HTTP request/response, double check the request parameters for any possible vulnerability like easy-to-guess tokens and invalid input, and scrutinise the client-side JavaScript code for potential issues.\

* From a secure coder perspective, ensuring that the token generation methods generate extremely unique and hard-to-guess tokens is essential.&#x20;
* The bank IT team quickly identified the issue and updated the token generation algorithm to remain unique and hard to predict.
* Open Josh's mailbox and navigate to the attacker's email with a green badge titled Urgent: Action Required - Suspicious Login Attempt Detected:

![select email screenshot](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/abe53a1e9bc6136f9c361171ad496650.png)\


* Using the same payload will not work because the server has correctly implemented a secure and unpredictable CSRF token that won't allow requests from an unknown source.

![protected from csrf - invalid attempt](https://tryhackme-images.s3.amazonaws.com/user-uploads/62a7685ca6e7ce005d3f3afe/room-content/b72585407d23eae854a1cc97ca7ad223.png)\






