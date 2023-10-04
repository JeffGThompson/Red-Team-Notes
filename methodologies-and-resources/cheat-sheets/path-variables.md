# PATH Variables

Look at the paths, below is an example of a cronjob. it first looks at /home/lachlan/bin before /bin and /usr/bin. therefore we can change pkill because it doesn't use the exact path in the cronjob. This means we can put a new file called /home/lachlan/bin/pkill with whatever we want and it will run as root.&#x20;

<figure><img src="../../.gitbook/assets/image (343).png" alt=""><figcaption></figcaption></figure>
