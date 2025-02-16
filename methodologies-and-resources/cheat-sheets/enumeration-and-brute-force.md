# Enumeration & Brute Force

## Enumerating Users via Verbose Errors

#### Understanding Verbose Errors

![Map icon](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719928819961.png)

Imagine you're a detective with a knack for spotting clues that others might overlook. In the world of web development, verbose errors are like unintentional whispers of a system, revealing secrets meant to be kept hidden. These detailed error messages are invaluable during the debugging process, helping developers understand exactly what went wrong. However, just like an overheard conversation might reveal too much, these verbose errors can unintentionally expose sensitive data to those who know how to listen.

Verbose errors can turn into a goldmine of information, providing insights such as:

* **Internal Paths**: Like a map leading to hidden treasure, these reveal the file paths and directory structures of the application server which might contain configuration files or secret keys that aren't visible to a normal user.
* **Database Details**: Offering a sneak peek into the database, these errors might spill secrets like table names and column details.
* **User Information**: Sometimes, these errors can even hint at usernames or other personal data, providing clues that are crucial for further investigation.

#### Inducing Verbose Errors

Attackers induce verbose errors as a way to force the application to reveal its secrets. Below are some common techniques used to provoke these errors:

1. **Invalid Login Attempts**: This is like knocking on every door to see which one will open. By intentionally entering incorrect usernames or passwords, attackers can trigger error messages that help distinguish between valid and invalid usernames. For example, entering a username that doesn’t exist might trigger a different error message than entering one that does, revealing which usernames are active.
2. **SQL Injection**: This technique involves slipping malicious SQL commands into entry fields, hoping the system will stumble and reveal information about its database structure. For example, placing a single quote ( `'`) in a login field might cause the database to throw an error, inadvertently exposing details about its schema.
3. **File Inclusion/Path Traversal**: By manipulating file paths, attackers can attempt to access restricted files, coaxing the system into errors that reveal internal paths. For example, using directory traversal sequences like `../../` could lead to errors that disclose restricted file paths.
4. **Form Manipulation**: Tweaking form fields or parameters can trick the application into displaying errors that disclose backend logic or sensitive user information. For example, altering hidden form fields to trigger validation errors might reveal insights into the expected data format or structure.
5. **Application Fuzzing**: Sending unexpected inputs to various parts of the application to see how it reacts can help identify weak points. For example, tools like Burp Suite Intruder are used to automate the process, bombarding the application with varied payloads to see which ones provoke informative errors.

#### The Role of Enumeration and Brute Forcing

When it comes to breaching authentication, enumeration and brute forcing often go hand in hand:

* **User Enumeration**: Discovering valid usernames sets the stage, reducing the guesswork in subsequent brute-force attacks.
* **Exploiting Verbose Errors**: The insights gained from these errors can illuminate aspects like password policies and account lockout mechanisms, paving the way for more effective brute-force strategies.

In summary, verbose errors are like breadcrumbs leading attackers deeper into the system, providing them with the insights needed to tailor their strategies and potentially compromise security in ways that could go undetected until it’s too late.

#### Enumeration in Authentication Forms

In this HackerOne [report](https://hackerone.com/reports/1166054), the attacker was able to enumerate users using the website's Forget Password function. Similarly, we can also enumerate emails in login forms. For example, navigate to [http://enum.thm/labs/verbose\_login/](http://enum.thm/labs/verbose_login/) and put any email address in the Email input field.

When you input an invalid email, the website will respond with "Email does not exist." indicating that the email has not been registered yet.

![Email does not exist error message](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1718887845395)

However, if the email is already registered, the website will respond with an "Invalid password" error message, indicating that the email exists in the database but the password is incorrect.

![Invalid password error message](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1718887844791)

#### Automation

Below is a Python script that will check for valid emails in the target web app. Save the code below as script.py.

```python
import requests
import sys

def check_email(email):
    url = 'http://enum.thm/labs/verbose_login/functions.php'  # Location of the login function
    headers = {
        'Host': 'enum.thm',
        'User-Agent': 'Mozilla/5.0 (X11; Linux aarch64; rv:102.0) Gecko/20100101 Firefox/102.0',
        'Accept': 'application/json, text/javascript, */*; q=0.01',
        'Accept-Language': 'en-US,en;q=0.5',
        'Accept-Encoding': 'gzip, deflate',
        'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8',
        'X-Requested-With': 'XMLHttpRequest',
        'Origin': 'http://enum.thm',
        'Connection': 'close',
        'Referer': 'http://enum.thm/labs/verbose_login/',
    }
    data = {
        'username': email,
        'password': 'password',  # Use a random password as we are only checking the email
        'function': 'login'
    }

    response = requests.post(url, headers=headers, data=data)
    return response.json()

def enumerate_emails(email_file):
    valid_emails = []
    invalid_error = "Email does not exist"  # Error message for invalid emails

    with open(email_file, 'r') as file:
        emails = file.readlines()

    for email in emails:
        email = email.strip()  # Remove any leading/trailing whitespace
        if email:
            response_json = check_email(email)
            if response_json['status'] == 'error' and invalid_error in response_json['message']:
                print(f"[INVALID] {email}")
            else:
                print(f"[VALID] {email}")
                valid_emails.append(email)

    return valid_emails

if __name__ == "__main__":
    if len(sys.argv) != 2:
        print("Usage: python3 script.py <email_list_file>")
        sys.exit(1)

    email_file = sys.argv[1]

    valid_emails = enumerate_emails(email_file)

    print("\nValid emails found:")
    for valid_email in valid_emails:
        print(valid_email)
```

<details>

<summary>Click here for a breakdown of the script.</summary>

* ```python
  ```

- ```python
  ```
- ```python
  ```

* ```python
  ```
* ```python
  ```

- ```python
  ```

* ```python
  ```

- ```python
  ```

* ```python
  ```

</details>

We can use a common list of emails from this [repository](https://github.com/nyxgeek/username-lists/blob/master/usernames-top100/usernames_gmail.com.txt).

![Usernames list from github](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1720084910130.png)\


Once you've downloaded the payload list, use the script on the AttackBox or your own machine to check for valid email addresses.



**Kali**

```
python3 script.py  usernames_gmail.com.txt 
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Exploiting Vulnerable Password Reset Logic

#### Password Reset Flow Vulnerabilities

Password reset mechanism is an important part of user convenience in modern web applications. However, their implementation requires careful security considerations because poorly secured password reset processes can be easily exploited.

**Email-Based Reset**

When a user resets their password, the application sends an email containing a reset link or a token to the user’s registered email address. The user then clicks on this link, which directs them to a page where they can enter a new password and confirm it, or a system will automatically generate a new password for the user. This method relies heavily on the security of the user's email account and the secrecy of the link or token sent.

**Security Question-Based Reset**

This involves the user answering a series of pre-configured security questions they had set up when creating their account. If the answers are correct, the system allows the user to proceed with resetting their password. While this method adds a layer of security by requiring information only the user should know, it can be compromised if an attacker gains access to personally identifiable information (PII), which can sometimes be easily found or guessed.

**SMS-Based Reset**

This functions similarly to email-based reset but uses SMS to deliver a reset code or link directly to the user’s mobile phone. Once the user receives the code, they can enter it on the provided webpage to access the password reset functionality. This method assumes that access to the user's phone is secure, but it can be vulnerable to SIM swapping attacks or intercepts.

Each of these methods has its vulnerabilities:

* **Predictable Tokens**: If the reset tokens used in links or SMS messages are predictable or follow a sequential pattern, attackers might guess or brute-force their way to generate valid reset URLs.
* **Token Expiration Issues**: Tokens that remain valid for too long or do not expire immediately after use provide a window of opportunity for attackers. It’s crucial that tokens expire swiftly to limit this window.
* **Insufficient Validation**: The mechanisms for verifying a user’s identity, like security questions or email-based authentication, might be weak and susceptible to exploitation if the questions are too common or the email account is compromised.
* **Information Disclosure**: Any error message that specifies whether an email address or username is registered can inadvertently help attackers in their enumeration efforts, confirming the existence of accounts.
* **Insecure Transport**: The transmission of reset links or tokens over non-HTTPS connections can expose these critical elements to interception by network eavesdroppers.

#### Exploiting Predictable Tokens

Tokens that are simple, predictable, or have long expiration times can be particularly vulnerable to interception or brute force. For example, the below code is used by the vulnerable application hosted in the Predictable Tokens lab:

```php
$token = mt_rand(100, 200);
$query = $conn->prepare("UPDATE users SET reset_token = ? WHERE email = ?");
$query->bind_param("ss", $token, $email);
$query->execute();
```

The code above sets a random three-digit PIN as the reset token of the submitted email. Since this token doesn't employ mixed characters, it can be easily brute-forced.

To demonstrate this, go to [http://enum.thm/labs/predictable\_tokens/](http://enum.thm/labs/predictable_tokens/).

![Predictable tokens login](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719133556444)\


Navigate to the application's password reset page, input "admin@admin.com" in the Email input field, and click Submit.

![Application reset page](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719133557936)\


The application will respond with a success message.

![Password reset link has been sent message](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719133558420)\


For demonstration purposes, the web application uses the reset link: `http://enum.thm/labs/predictable_tokens/reset_password.php?token=123`

\


<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1721645083625.png" alt=""><figcaption></figcaption></figure>

Notice the token is a simple numeric value. Using Burp Suite, navigate to the above URL and capture the request.

Subsequently, send the request to the Intruder, highlight the value of the token parameter, and click the Add payload button, as shown below.

\


<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719133559379" alt=""><figcaption></figcaption></figure>

Using the AttackBox or your own attacking VM, use Crunch to generate a list of numbers from 100 to 200. This list will be used as the dictionary in the brute-force attack.

**Kali**

```shell-session
crunch 3 3 -o otp.txt -t %%% -s 100 -e 200             
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Go back to Intruder and configure the payload to use the generated file.

![otp.txt in the intruder](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719134235270)

![showing otp.txt loaded](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1722386680524.png)\


The attack will take some time to finish if you're using Burp Suite Community Edition. However, once successful, you will get a response with a much bigger content length compared to the responses with an "Invalid token" error message.

![Response with the biggest response size](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719134236525)

Log in to the application using the new password.

## Exploiting HTTP Basic Authentication

#### Basic Authentication in 2k24?

Basic authentication offers a more straightforward method when securing access to devices. It requires only a username and password, making it easy to implement and manage on devices with limited processing capabilities. Network devices such as routers typically utilise basic authentication to control access to their administrative interfaces. In this scenario, the primary goal is to prevent unauthorized access with minimal setup.

While basic authentication does not offer the robust security features provided by more complex schemes like OAuth or token-based authentication, its simplicity makes it suitable for environments where session management and user tracking are not required or are managed differently. For example, in devices like routers that are primarily accessed for configuration changes rather than regular use, the overhead of maintaining session states is unnecessary and could complicate device performance.

HTTP Basic Authentication is defined in [RFC 7617](https://datatracker.ietf.org/doc/html/rfc7617), which specifies that the credentials (username and password) should be transported as a base64-encoded string within the HTTP Authorization header. This method is straightforward but not secure over non-HTTPS connections, as base64 is not an encryption method and can be easily decoded. The real threat often comes from weak credentials that can be brute-forced.

HTTP Basic Authentication provides a simple challenge-response mechanism for requesting user credentials.

![Basic authentication login process](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719037051793)

_https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication_\


The Authorization header format is as follows:

```html
Authorization: Basic <credentials>
```

where `<credentials>` is the base64 encoding of `username:password`. For detailed specifications, refer to [RFC 7617](https://tools.ietf.org/html/rfc7617).

#### Exploitation

To demonstrate this, go to [http://enum.thm/labs/basic\_auth/](http://enum.thm/labs/basic_auth/).

![Basic Authentication Lab Homepage](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719138339394)\


Input any username and password in the pop-up and capture the Basic Auth Request using Burp.

![Random login credentials in the popup](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719138339820)

![Encoded login credentials in the HTTP Request Header](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719138340268)\


Right-click the request and send it to Intruder.

![Captured request context menu](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719623302038.png)\


*

In Burp Intruder, go to the "Positions" tab and decode the base64 encoded string in the Authorization header.

![Decoded base64 encoded string](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719623303064.png)\


*

Once decoded, highlight the decoded string and click the Add button in the top right corner.

![Highlighted text with a payload highlight](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719623303378.png)\


*

Next is configuring the payloads. Go to the Payloads tab and set the payload type to Simple list and choose your preferred wordlist. In this demo, we will use the [500-worst-passwords.txt](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/500-worst-passwords.txt) from SecLists. If you're using the AttackBox, you may use the same wordlist located at `/usr/share/wordlists/SecLists/Passwords/Common-Credentials/500-worst-passwords.txt`.

![Choosing a wordlist](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719624218420.png)

Since the header is base64 encoded, we need to add two rules in the Payload processing section. The first automatically adds a username to the password, so instead of 123456, the payload will be "admin:123456".

![First payload processing rule](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1721558974360.png)\


The second rule will base64 encode the combined username and password from the supplied list.

![Second payload processing rule](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1721559029302.png)\


We should also remove the character "=" (equal sign) from the encoding because base64 uses "=" for padding. To do this, scroll down and remove the "=" sign from the list of characters in the Payload encoding section.

![Removing the equal sign due to padding](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1721559089710.png)\


Once done, go back to the Positions tab and click the Start Attack button. The attack will take a little less than 2 minutes.

![Starting the attack](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719624746210.png)

Once you get a Status code 200, it means the brute force is successful, and one of the passwords in the supplied list is working. Decode the encoded base64 string in the successful request.

![Decoding the payload of the successful request](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719624746716.png)

![Decoded value of the base64 string](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719624747204.png)

Use the decoded base64 string to log into the application.

![Using the credentials to login](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719624882640.png)

![Flag in the dashboard](https://tryhackme-images.s3.amazonaws.com/user-uploads/645b19f5d5848d004ab9c9e2/room-content/645b19f5d5848d004ab9c9e2-1719624883193.png)\


### Hydra

**Kali**

```
hydra -l admin -P 500-worst-passwords.txt http-get:"//enum.thm/labs/basic_auth/:A:Basic" -V
```

