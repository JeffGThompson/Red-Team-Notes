# LFI / RFI

## Places to look if LFI is possible

| Location                    | Description                                                                                                                                                       |
| --------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| /etc/issue                  | contains a message or system identification to be printed before the login prompt.                                                                                |
| /etc/profile                | controls system-wide default variables, such as Export variables, File creation mask (umask), Terminal types, Mail messages to indicate when new mail has arrived |
| /proc/version               | specifies the version of the Linux kernel                                                                                                                         |
| /etc/passwd                 | has all registered user that has access to a system                                                                                                               |
| /etc/shadow                 | contains information about the system's users' passwords                                                                                                          |
| /root/.bash\_history        | contains the history commands for root user                                                                                                                       |
| /var/log/dmessage           | contains global system messages, including the messages that are logged during system startup                                                                     |
| /var/mail/root              | all emails for root user                                                                                                                                          |
| /root/.ssh/id\_rsa          | Private SSH keys for a root or any known valid user on the server                                                                                                 |
| /var/log/apache2/access.log | the accessed requests for Apache webserver                                                                                                                        |
| C:\boot.ini                 | contains the boot options for computers with BIOS firmware                                                                                                        |
|                             |                                                                                                                                                                   |

**Bypasses**

| Bypass                 | Explanation                                                                                                                                                                                                                                                     | Example                                                          |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- |
| Bypass extention added | Using null bytes is an injection technique where URL-encoded representation such as %00 or 0x00 in hex with user-supplied data to terminate strings. You could think of it as trying to trick the web app into disregarding whatever comes after the Null Byte. | hxxp://10.10.230.14/lab3.php?file=../../../../../etc/passwd%00   |
| PHP removing slashes   | This works because the PHP filter only matches and replaces the first subset string ../ it finds and doesn't do another pass.                                                                                                                                   | hxxp://10.10.230.14/lab3.php?file=..//..//..//..//..//etc/passwd |
|                        |                                                                                                                                                                                                                                                                 |                                                                  |
