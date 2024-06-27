---
author: "0xt0pus"
title: "HackTheBox Lame Machine WriteUps"
date: "2023-09-07"
description: "A beginner track machine from HackTheBox"
tags: ["Hackthebox", "Lame", "Machine-Walkthrough"]
cover:
    image: "/writeups/Lame-Hackthebox/lame.png"
ShowToc: true
TocOpen: false
---


## Enumeration

Initially I tried pinging the IP address. It is reachable.

```bash
┌──(kali㉿kali)-[~]
└─$ ping 10.10.10.3 -c 3
PING 10.10.10.3 (10.10.10.3) 56(84) bytes of data.
64 bytes from 10.10.10.3: icmp_seq=1 ttl=63 time=173 ms
64 bytes from 10.10.10.3: icmp_seq=2 ttl=63 time=189 ms
64 bytes from 10.10.10.3: icmp_seq=3 ttl=63 time=172 ms

--- 10.10.10.3 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
rtt min/avg/max/mdev = 171.906/178.070/189.264/7.928 ms
```

I run nmap all ports on it for getting to know the open ports. It shows that the server is down.

```bash
┌──(kali㉿kali)-[~]
└─$ nmap 10.10.10.3 -p- --min-rate 2500 -T4 
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-03 10:15 PST
Note: Host seems down. If it is really up, but blocking our ping probes, try -Pn
Nmap done: 1 IP address (0 hosts up) scanned in 1.05 seconds
```

Maybe it was not accepting the ping requests so I used -Pn.

Now it worked and shows the following results.

```bash
┌──(kali㉿kali)-[~]
└─$ nmap 10.10.10.3 -p- --min-rate 2500 -T4  -Pn
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-03 10:16 PST
Nmap scan report for 10.10.10.3
Host is up (0.18s latency).
Not shown: 65530 filtered tcp ports (no-response)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3632/tcp open  distccd

Nmap done: 1 IP address (1 host up) scanned in 52.88 seconds
```

Now I separated the open ports as

```bash
21,22,139,445,3632
```

Now I can add these ports for Service version enumeration.

```bash
┌──(kali㉿kali)-[~/Desktop/HackTheBox/lame]
└─$ nmap -p 21,22,139,445,3632 10.10.10.3 -sC -sV -oN nmapSVscan.txt -Pn
Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-03 10:36 PST
Stats: 0:00:13 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 100.00% done; ETC: 10:37 (0:00:00 remaining)
Nmap scan report for 10.10.10.3
Host is up (0.29s latency).

PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.16.6
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 600fcfe1c05f6a74d69024fac4d56ccd (DSA)
|_  2048 5656240f211ddea72bae61b1243de8f3 (RSA)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
3632/tcp open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 2h30m24s, deviation: 3h32m10s, median: 22s
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: lame
|   NetBIOS computer name: 
|   Domain name: hackthebox.gr
|   FQDN: lame.hackthebox.gr
|_  System time: 2023-03-03T13:37:39-05:00
|_smb2-time: Protocol negotiation failed (SMB2)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 56.19 seconds
```

## Exploitation

The **vsftpd 2.3.4** seems vulnerable. I quicly googled the version and got the it can be exploited with Metasploit. You can check here the [guide](https://www.rapid7.com/db/modules/exploit/unix/ftp/vsftpd_234_backdoor/).

I opened up the Metasploit, searched for vsftpd and set the options as shown below.

![](/writeups/Lame-Hackthebox/1.png)


I failed couple of time, so I stopped further trying the vsftpd.

I moved to the next potential vulnerability, which i came across when i googled the version of smbd. Which is **smbd 3.0.20.** I found the [following](https://www.rapid7.com/db/modules/exploit/multi/samba/usermap_script/), According to which, we can use metasploit to exploit this.

I tried exploiting this, as shown below.

![](/writeups/Lame-Hackthebox/2.png)


Yes, it worked. So I got root access. That was it.

## Conclusion

The **smbd 3.0.20** was outdated and was vulnerable, which we exploited with Metasploit.

Get more updates and follow me on LinkedIn.

LinkedIn Link: [https://www.linkedin.com/in/muhammadyqb/](https://www.linkedin.com/in/muhammadyqb/)

Thank You!!