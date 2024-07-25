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



