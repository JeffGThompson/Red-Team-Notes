# File Info Gathering & Script Abuse

## **Gather info from script or file**

If it's a file we can't read like a binary file we might be able to still gather some info of how it works or maybe even credentials.

**Examples**

[valley.md](../../walkthroughs/tryhackme/valley.md "mention")

**Kali**

```
strings $FILE > out.txt
```



## Abusing Library paths&#x20;

**Examples**

[wonderland.md](../../walkthroughs/tryhackme/wonderland.md "mention")[opacity.md](../../walkthroughs/tryhackme/opacity.md "mention")[valley.md](../../walkthroughs/tryhackme/valley.md "mention")

If a script is using libraries check if the paths can be abused. It may be possible to instead of importing the library to go to a script we create instead or modify the existing one if we have access to do so.

Check the script and which libraries it uses.

<figure><img src="../../.gitbook/assets/image (308).png" alt=""><figcaption></figcaption></figure>

Check to see what takes precedence. For example in the screenshot below it says ' ' has the highest priority which means the current working directory.

**Victim**

```
python3 -c 'import sys; print (sys.path)'
locate $FILE
```

<figure><img src="../../.gitbook/assets/image (10) (11) (1).png" alt=""><figcaption></figcaption></figure>

Check if we have access to modify any of the libraries' that the script uses.

**Victim**

```
locate $FILE
ls -lah /path/to/file/$FILE
groups
```





**Kali**

```
cd db
cat joomladb.sql | grep admin
```

## Ghidra

[#ghidra](../../walkthroughs/tryhackme/madeyes-castle/#ghidra "mention")[#ghidra](../../walkthroughs/tryhackme/wonderland.md#ghidra "mention")[linux-agency.md](../../walkthroughs/tryhackme/linux-agency.md "mention")[#ghidra](../../walkthroughs/tryhackme/tokyo-ghoul.md#ghidra "mention")[#ghidra](../../walkthroughs/tryhackme/bookstore.md#ghidra "mention")

