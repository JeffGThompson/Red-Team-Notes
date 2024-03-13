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













