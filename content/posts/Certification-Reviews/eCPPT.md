---
author: "0xt0pus"
title: "eCPPT Exam Review"
date: "2024-02-04"
description: "eLearn Security Certified Professional Penetration Tester (eCPPT) is a Network penetration testing certification by INE (formerly known as eLearnSecurity)."
tags: ["eCPPT", "Penetration Testing", "Certification"]
cover:
    image: "/certification/ecppt/ecppt.png"
ShowToc: true
TocOpen: false
---



## What is eCPPT?

eLearn Security Certified Professional Penetration Tester (eCPPT) is a Network penetration testing certification by INE. This exam proves that certified professionals have adequate knowledge to perform Penetration Testing on the network (multiple hosts and servers) and can provide the documentation of the findings. 

| Price              | 400$ (Without training)                                              |
| ------------------ | -------------------------------------------------------------------- |
| Training Cost      | 749$                                                                 |
| Passing score      | (Exploitation of all the machines and a good report)                 |
| Allowed time       | 7 days for Pentesting and 7 days for report                          |
| Proctored?         | No                                                                   |
| Link to purchase   | [Here](https://security.ine.com/certifications/ecppt-certification/) |
| Exam Focused Areas | Pivoting, Buffer Overflows                                           |

## My Experience:

I started the eCPPT exam in the morning of 25th February 2024, uploaded report on 7th march at 1PM and got the email with subject *"You are now an eCPPTv2!"* on 27th March 2024.

As I prepared with the INE official course material, so before talking about the exam, I would like to talk about the course material.  

The course covers all the skills needed to pass the eCPPT exam. However, I would say the course has over loaded information, which personally I skipped a lot. It is beneficial for the beginner to understand the concept but that's going to take lot of time. In some places, you will find outdated information in the course, as the course is a little bit older (Thanks to INE that in April they will release new course material for eCPPT). So, Can we see eCPPT V3 soon? 

## The VPN Issue 

When I started the exam, the VPN was not working. It was giving the following error: 

```bash
Reducted...


failed to negotiate cipher with server


Reducted...
```


In the given openvpn configuration file:

The following line was there:

```bash
cipher AES-128-CBC
```

The above line was changed as

```bash
data-ciphers AES-128-CBC
```

This fixed the issues. Further details about the issue can be found here [here](https://github.com/OpenVPN/openvpn-gui/issues/381).

## Preparation:

I started the exam on 25 February and submitted the report on 7th march 2024. It took me 12 days to submit the report, because I was also attending my university classes during this time. If you give full time to it, you may finish it earlier.  I received the result after a long wait on 27 March 2024. INE has to work to reduce the exam result time. INE should also reduce the long 14 days time of the exam. The reason I have not attempted it for 1 year was lack of time, because I was always busy with work and studies that I was unable to take 15 days break from all that. 

## Timeline 

**Day1**: 
In the first day, I was able to pwn one machine.  It was very easy and I was happy and confident.

**Day2**: 
I faced issues in setting the pivoting and proxychains during this day, and by the end of the day I was only able to verify that I set up pivoting the right way and all my tools started working again in this environment.

**Day3**: 
I pawned the second machine fairly easily. I tried all what I can to pwn the 3rd machine but I failed, I was thinking it very complex but it was very simple, just lack of post exploitation enumeration. 

**Day4**: 
In the first half, I stuck again and at this point, I started worrying. But when I enumerated with focus then I found that I was missing a very simple thing, it was not needing anything extra. I rooted the 3rd machine and also worked on the 4rth one, which was the buffer overflow one. 

**Day5**: 
I managed to generate a working exploit for the buffer overflow machine.  Note that, whenever you send a long input, the backend application will crash and buffer overflow machine will become unresponsive, you will need to reset the whole exam environment to make it responsive again, in this case make sure to use this tag while generating payload from the msfvenom `EXITFUNC=thread`. On the same day, I exploited the 5th and last machine as well. 


## Reporting

It is the most important part of the exam. You will not get passing marks if you only show the ways of getting root access across all the machines, but you have to report all the vulnerabilities in each of the machine. I used TCM security report template, which I found to be more beneficial than others. My report was 130 pages long. I have not taken and saved screenshots during the exam, because I started recording using OBS at the start of the exam and saved all the recording with me. I went through all the videos later while preparing the report and took screenshot of the part in the video to add in the report. 


## Tips for the exam:
- Work on the pivoting before going to attempt the exam, it is mandatory. 
- Practice the buffer overflows in the windows environment. 
- Get your hands dirty on Metasploit. Specially the post exploitation and routing. 
- Enumerate well in the post exploitation phase.
- Instead of making screenshot of everything, use OBS to record screen while working. You can use those recorded videos to make screen shots later while reporting.  

## Resources:

- [THM Wreath Room for Pivoting](https://tryhackme.com/r/room/wreath)
- [Home Lab setup for pivoting](https://www.youtube.com/watch?v=QNoIX1au_CM)
- [Buffer Overflows practice](https://www.youtube.com/watch?v=ALbNTOOqmsA)
- [Double Pivoting](https://pentest.blog/explore-hidden-networks-with-double-pivoting/)
- [Practice Pivoting with Metasploit](https://www.offsec.com/metasploit-unleashed/pivoting/)
- [TCM Report Template for Reporting](https://github.com/hmaverickadams/TCM-Security-Sample-Pentest-Report)

