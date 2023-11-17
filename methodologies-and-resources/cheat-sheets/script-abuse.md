# Script Abuse

## Abusing Paths

If a script is running a command without using the full path you may be able to redirect that to another script by adding a new location to your path.

**Example:**

[wonderland.md](../../walkthroughs/tryhackme/wonderland.md "mention")



## Abusing Library path&#x20;

If a script is using libraries check if the paths can be abused. It may be possible to instead of importing the library to go to a script we create instead.&#x20;

**Victim**

```
python3 -c 'import sys; print (sys.path)'
locate random.py
```

**Example:**

[wonderland.md](../../walkthroughs/tryhackme/wonderland.md "mention")

