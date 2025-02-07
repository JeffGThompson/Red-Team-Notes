# Bypass

**Room Link:** [https://tryhackme.com/r/room/bypass](https://tryhackme.com/r/room/bypass)



What is the flag value after accessing the endpoint cctv.thm/fpassword.php?id=1?

**Kali**

```
nmap -A cctv.thm
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
ffuf -u https://cctv.thm/FUZZ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/raft-medium-directories-lowercase.txt
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I first tried using knock but it wasn't working, probably because it's not actually sending a message.

**Kali**

```
git clone https://github.com/grongor/knock.git
cd knock
./knock cctv.thm 5000:udp -v
```

**knock.py**

```
import socket

port = 5000
host = 'cctv.thm'
msg = 'Knock,Knock...TheSysRat is here!'.encode()

try:
    s = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    s.bind(('', 5000))
    s.sendto(msg, (host, port))
    s.close()
    print('Packet send correctly')
except:
    print('Something wrong')
```

**Kali**

```
python knock.py 
```

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



What is the flag value after accessing the endpoint cctv.thm/fpassword.php?id=2?

**knock.py**

```
import socket
import ssl

# Target server and port
host = "cctv.thm"
port = 443  # HTTPS port for TCP request

# Target path
path = "/fpassword.php?id=2"

# Custom headers with User-Agent
request = f"GET {path} HTTP/1.1\r\n" \
          f"Host: {host}\r\n" \
          f"User-Agent: I am Steve Friend\r\n" \
          f"Connection: close\r\n\r\n"

# Create an SSL context that ignores certificate verification
context = ssl.create_default_context()
context.check_hostname = False
context.verify_mode = ssl.CERT_NONE

# Create a TCP socket and wrap it in SSL
with socket.create_connection((host, port)) as sock:
    with context.wrap_socket(sock, server_hostname=host) as ssock:
        # Send the request
        ssock.sendall(request.encode())

        # Receive the response
        response = b""
        while True:
            data = ssock.recv(4096)
            if not data:
                break
            response += data

# Decode and print the response to get the flag
print(response.decode())

```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



What is the flag value after accessing the endpoint cctv.thm/fpassword.php?id=3?



What is the flag value after accessing the endpoint cctv.thm/fpassword.php?id=4?



What is the flag value after accessing the endpoint cctv.thm/fpassword.php?id=5?



What is the password value for the first layer of security for the CCTV web panel?



What is the lsb\_release -r -s command output from the attached machine?



What is the username for the CCTV web panel?



What is the flag value after logging into the CCTV web panel?



