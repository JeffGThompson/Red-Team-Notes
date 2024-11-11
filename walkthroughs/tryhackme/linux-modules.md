# Linux Modules

**Room Link:** [https://tryhackme.com/r/room/linuxmodules](https://tryhackme.com/r/room/linuxmodules)

## du

`du` is a command in linux (short for disk usage) which helps you identify what files/directories are consuming how much space. If you run a simple du command in terminal...

![Terminal output after running the du command, showing size and path>\<br>\</p>\<p style=](https://tryhackme-images.s3.amazonaws.com/user-uploads/5eff6381a8b8f6323ba744fe/room-content/c378db9fe2baa7d2a42b59d2b87b58b9.png)

The folders in their respective folders are listed here with the size they occupy on the disk. The size here is shown in KB. Note: The files inside a folder are not shown, only the folders are listed by running du /\<directory> command.\


**Important flags**

| Flag         | Description                                                                                                           |
| ------------ | --------------------------------------------------------------------------------------------------------------------- |
| -a           | Will list files as well with the folder.                                                                              |
| -h           | Will list the file sizes in human readable format(B,MB,KB,GB)                                                         |
| -c           | Using this flag will print the total size at the end. Jic you want to find the size of directory you were enumerating |
| -d \<number> | Flag to specify the depth-ness of a directory you want to view the results for (eg. -d 2)                             |
| --time       | To get the results with time stamp of last modified.                                                                  |

**Examples**

`du -a /home/` will list every file in the /home/ directory with their sizes in KB.

If there's a lot of output you can surely use grep...

`du -a /home/ | grep user` will list any file/directory whose name is containing the string "_user_" in it.

du command can alternate `ls` with the following flags:

`du --time -d 1 .`

It won't specify you the user ownership though, so you can use `stat` command on the file you want to know who is the owner of that particular file&#x20;

Syntax: `stat`

## Grep, Egrep, Fgrep

**Introduction**

It is a must known tool to everyone and that's why linux modules won't be complete without doing a mention of its amazing charisma. This tool, is what filters the good output we need from the residue. The official documentation says, The grep filter searches a file for a particular pattern of characters, and displays all lines that contain that pattern. The pattern that is searched in the file is referred to as the regular expression. The pattern is what I am gonna brief you about.

Syntax: `grep "PATTERN" file.txt` will search the file.txt for the specified "PATTERN" string, if the string is found in the line, the grep will return the whole line containing the "PATTERN" string.&#x20;

**The Family Tree**

egrep and fgrep are no different from grep(other than 2 flags that can be used with grep to function as both). In simple words, egrep matches the regular expressions in a string, and fgrep searches for a fixed string inside text. Now grep can do both their jobs by using -E and -F flag, respectively.

In other terms, `grep -E` functions same as `egrep` and `grep -F` functions same as `fgrep`.

**Important Flags**

| Flags | Description                                                                                                                                                                               |
| ----- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -R    | Does a recursive grep search for the files inside the folders(if found in the specified path for pattern search; else grep won't traverse diretory for searching the pattern you specify) |
| -h    | If you're grepping recursively in a directory, this flag disables the prefixing of filenames in the results.                                                                              |
| -c    | This flag won't list you the pattern only list an integer value, that how many times the pattern was found in the file/folder.                                                            |
| -i    | I prefer to use this flag most of the time, this is what specifies grep to search for the PATTERN while IGNORING the case                                                                 |
| -l    | will only list the filename instead of pattern found in it.                                                                                                                               |
| -n    | It will list the lines with their line number in the file containing the pattern.                                                                                                         |
| -v    | This flag prints all the lines that are NOT containing the pattern                                                                                                                        |
| -E    | This flag we already read above... will consider the PATTERN as a regular expression to find the matching strings.                                                                        |
| -e    | The official documentation says, it can be used to specify multiple patterns and if any string matches with the pattern(s) it will list it.                                               |

You might be wondering the difference between -E and -e flag. I suggest to understand this as the following:

* \-e flag can be used to specify multiple patterns, with multiple use of -e flag( grep -e PATTERN1 -e PATTERN2 -e PATTERN3 file.txt), whereas, -E can be used to specify one single pattern(You can't use -E multiple times within a single grep statement).

Other point that you can use to understand the difference is, -e works on the BREs(Basic Regular Expressions) and -E works on EREs (Extended Regular Expressions).

* BREs tend to match a single pattern in a file (Simplest examples can be direct words like "sun", "comic")
* EREs tend to match 2 or more patterns in a file (To select a no of words like (sun sunyon sandston) the pattern could be "^s.\*n$").&#x20;

Hope, you get an idea how this works.&#x20;

Here's a real short note, you might wanna read, on official GNU documentation: [Basic vs Extended (GNU Grep 3.5)](https://www.gnu.org/software/grep/manual/html\_node/Basic-vs-Extended.html). If you didn't understand much from that paragraph, make sure, you've practiced your regex well.&#x20;

### Answer the questions

**Is there a difference between egrep and fgrep? (Yea/Nay)**

```
Nay
```

**Which flag do you use to list out all the lines NOT containing the 'PATTERN'?**

```
-v
```

**What user did you find in that file?**

```
grep -i user grep_1611752025618.txt 
```

<figure><img src="../../.gitbook/assets/image (1203).png" alt=""><figcaption></figcaption></figure>

**What is the password of that user?**

```
grep -i pass grep_1611752025618.txt 
```

<figure><img src="../../.gitbook/assets/image (1204).png" alt=""><figcaption></figcaption></figure>

**Can you find the comment that user just left?**

```
grep -i comment grep_1611752025618.txt 
```

<figure><img src="../../.gitbook/assets/image (1205).png" alt=""><figcaption></figcaption></figure>

## tr

Translate command(`tr`) can help you in number of ways, ranging from changing character cases in a string to replacing characters in a string. It's awesome at it's usage. Plus, it's the easiest command and a must know module for quick operations on strings.

Syntax: `tr [flags] [source]/[find]/[select] [destination]/[replace]/[change]`

This I guess is an appropriate representation of how you can use this tool. Moreover, we have the following flags offered by this command:

| Flags | Description                                                                                                                                                                                                                                                 |
| ----- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -d    | To delete a given set of characters                                                                                                                                                                                                                         |
| -t    | To concat source set with destination set(destination set comes first; t stands for truncate)                                                                                                                                                               |
| -s    | To replace the source set with the destination set(s stands for squeeze)                                                                                                                                                                                    |
| -c    | This is the REVERSE card in this game, for eg. If you specify -c with -d to delete a set of characters then it will delete the rest of the characters leaving the source set which we specified (c stands for complement; as in doing reverse of something) |

You must have noticed the word "set" while reading the flags. Well that's true... tr command works in sets of character.

Examples

* If you want to convert every alphabetic character to upper case.

<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/5eff6381a8b8f6323ba744fe/room-content/4931e5b54dad039aad2737c99c3ffbb0.png" alt=""><figcaption></figcaption></figure>

Or I am not sure, if you ever used emojis on discord, coz on desktop app you could use emojis using :keyword:. Similarly, tr allows us to select a set by these keywords. In that case the output would be same.

`cat file.txt | tr -s '[:lower:]' '[:upper:]'`

There are more of these (interpreted sequences) which you can view, by just `tr --help` command. I am not including them here, because they are just straight forward, and you've been using most of them, if you're familiar with (mostly) any programming language out there.

* If you want to view creds of a user which are in digits.

<figure><img src="https://tryhackme-images.s3.amazonaws.com/user-uploads/5eff6381a8b8f6323ba744fe/room-content/801400336ac11dfc91e8ff255bf5b9dd.png" alt=""><figcaption></figcaption></figure>

You can see that I used regex here, and deleted all lower/upper case characters, including the (:) symbol and a space.

Note: This is a short note on how you can use this tool. Now try out these features on your own and get use to this tool. You can also refer to the following sites for more on the tool:

* [tr command in Unix/Linux with examples - GeeksforGeeks](https://www.geeksforgeeks.org/tr-command-in-unix-linux-with-examples/)
* [Tr Command in Linux with Examples | Linuxize](https://linuxize.com/post/linux-tr-command/)

### Answer the questions

**Run tr --help command and tell how will you select any digit character in the string?**

```
tr --help 
```

<figure><img src="../../.gitbook/assets/image (1206).png" alt=""><figcaption></figcaption></figure>

**What sequence is equivalent to \[a-zA-Z] set?**

<figure><img src="../../.gitbook/assets/image (1207).png" alt=""><figcaption></figcaption></figure>

**What sequence is equivalent to selecting hexadecimal characters?**

<figure><img src="../../.gitbook/assets/image (1208).png" alt=""><figcaption></figcaption></figure>













