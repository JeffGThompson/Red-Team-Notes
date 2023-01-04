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

