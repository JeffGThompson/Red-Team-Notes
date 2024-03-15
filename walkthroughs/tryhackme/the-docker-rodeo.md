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
![](https://i.imgur.com/rz4orgs.png)\
\
Not only is Nmap capable of discovering the Docker Registry, but also the API version - this is important to note for how we will interact with it.\
The Docker Registry is a JSON endpoint, so we cannot just simply interact with it like we would a normal website - we will have to query it. Whilst this can be done via the terminal or browser, dedicated tools such as [Postman ](https://www.postman.com/downloads/)or [Insomnia ](https://insomnia.rest/download/)are much better suited for the job. I will be using Postman in this room.\
To understand what routes are available to us, we need to read the [Docker Registry Documentation](https://docs.docker.com/registry/spec/api/). Please take the time to read this at your leisure.\
3.2.1. Discovering Repositories We need to send a `GET` request to `http://docker-rodeo.thm:5000/v2/_catalog` to list all the repositories registered on the registry.

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

![](https://resources.cmnatic.co.uk/TryHackMe/rooms/docker-rodeo/reversedockerimages/using-dive.png)\
\
4.5. Using DiveDive is a little overwhelming at first, however, it quickly makes sense. We have four different views, we are only interested in these three views:4.5.1. Layers (pictured in red)4.5.1.1. This window shows the various layers and stages the docker container has gone through4.6.1. Current Layer Contents (pictured in green)4.6.1.1. This window shows you the contents of the container's filesystem at the selected layer4.7.1. Layer Details (pictured in red)4.7.1.1. Shows miscellaneous information such as the ID of the layer and any command executed in the Dockerfile for that layer.\


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
