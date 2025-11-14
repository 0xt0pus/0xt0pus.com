---
author: "0xt0pus"
title: "Altered Security CRTP Review"
date: "2024-08-08"
description: "Certified Red Team Professional (CRTP) is beginner level red teaming certification focused on Active Directory."
tags: ["CRTP", "Active Directory", "Certification"]
cover:
    image: "/certification/crtp/crtp.png"
ShowToc: true
TocOpen: false
---


# What is CRTP?

Certified Red Team Professional (CRTP) is beginner level red teaming certification focused on Active Directory by Altered Security. This certification exam proves that certified professionals have sufficient knowledge to perform Red Teaming engagement on an Active Directory environment.

| Price              | $249 (With 30 Days Lab Access)                          |
| ------------------ | ------------------------------------------------------- |
| Passing Score      | OS command execution on all the five target servers.    |
| Allowed Time       | 25 hours exam time, and additional 48 hours for report. |
| Proctored?         | No                                                      |
| Link to Purchase   | [Here](https://www.alteredsecurity.com/adlab)           |
| Exam Focused Areas | Red Teaming, Active Directory, MDI Bypass               |


# Packages

Training is included with the certification exam. There are different packages depending on the lab access time and type of training. 
There are two types of training that can be followed.
1. Bootcamps:
		These are the instructor led bootcamps for 4 week. In this training, you can interact with the instructor and can follow along. 
2. On Demand:
		This is the self paced training, where the recordings of the previous bootcamp session will be provided as the training videos. You can watch those videos on your own at any time. 
		
For more information about different packages check the purchase option on the following link:
https://www.alteredsecurity.com/adlab


# My Experience with the Course

I chose the on-demand course with 30 days of lab access. I found the videos a bit lengthy compared to other courses I've taken. There's no LMS system; instead, you'll be provided with a Google Drive link to the videos. This is because, rather than focusing on the LMS platform, they have prioritized the quality of the training. The learning from those lengthy videos is simply amazing. Nikhil Mittal is a well-known figure in AD Security. He not only teaches the topic but also shares insights on whether an attack would be detected, what type of logging or security solution would catch a specific attack, and how to bypass those security measures. He also shares his experiences related to those attacks.	

In the course, the instructor first teaches the theory and then demonstrates how to solve the lab on their platform for the related topic. A great aspect is that you get lifetime access to the course material, which remains accessible even after the exam.

# My Experience with the Labs

In the lab, you will be given access to a student VM, which is a part of AD enviroment with multiple machines and forests. You need to apply what you've learned to access these machines in different ways.

The mistake I made was trying to do both the lab and the course simultaneously. Before starting the course, I began my lab timer, which was limited to 30 days. While progressing through the course, I completed a few of the initial labs. However, I then decided to focus on finishing the course first. Due to my busy schedule, I completed the course but couldn't finish all the labs in time. So, I recommend finishing the course at least once before starting the lab timer.

Fortunately, the lab manual with detailed commands, results, and walkthroughs was available even after the 30-day lab period expired, which was a great help. I used these resources to go through all the lab topics. 

# Exam Environment 

In the exam, you are given a student user account on a machine with low-level access. The goal of the exam is to achieve OS Command injection on five servers. The names of all the servers will be provided to you once you start the certification exam. There are unlimited restarts available, so you can restart any machine at any time. Make use of this often, as sometimes things start working after a machine is restarted.

You also have two chances to reset the entire exam environment, which will reset all the machines and provide you with fresh network instance, including the VPN configuration. I wouldn't recommend using this option unless you’ve encountered a serious issue, as the reset process takes 20-25 minutes, which would waste valuable time. Instead, use the quick restart option whenever possible.

# My Experience with the Exam

I started the exam in the morning, with hope that I could access all the machines by evening or bedtime. However, I was mistaken, as it took me a full 25 hours. I was extremely tired after 25 sleepless hours.

The first issue I encountered was bypassing the MDI. All my scripts were getting blocked since it was a fully patched Windows environment. I realized my mistake after some time and followed the process taught in the course to handle this.

Next, my goal was to gain elevated privileges on my own machine, which I managed to do quickly. I then found the vulnerability for the first target quickly but got stuck trying to find the correct way to exploit it. Follow the tips I’ve shared below to avoid this issue.

The second machine was easy, and I gained access very quickly. The third machine was very confusing for me and took more than 10-12 hours. This one requires some additional research, as I couldn’t get around it using the methods taught in the course. Eventually, I found a way to bypass it.

The fourth machine was also straightforward, and I completed it quickly. The fifth and final machine required accessing a machine in another domain. This was also easy when I followed the process taught in the course.

# Reporting
Once the exam time ended, I had 48 hours to submit the report. After the exam, the first thing I did was get some much-needed sleep. Then, I worked on the report slowly and finished it the next day. I emailed the report and, five days later, received an email starting with, 'Congratulations! You have cleared the examination! You are now a Certified Red Team Professional.'

# Tips for the Exam

- Try to have different Mimikatz versions (exe,ps1 & etc ), if one does not work, use the second one. 
- Always run Mimikatz with NT Authority System level access. It will throw errors sometimes even if you are administrator or in the administrator group. So always have NT Authority System level access and then run it. 
- If Mimikatz.ps1 gives error of 64 bit compatibility, use mimikatz.exe.
- Always try to run Rubeus.exe on cmd.exe not on powershell.exe without Invishell.
- When any script catch by MDI, use Download Execute Cradles. 
- Use DCSync on DC only, and simple credential dumping on machine after getting NT Authority System. 
- Password extraction from lsass process is definitely helpful, but never ignore creds vaults. 

I hope you learned something from this review and that these tips help you on your CRTP journey. I wish you the best of luck on your Active Directory penetration testing journey. Lets get connected on [LinkedIn](https://linkedin.com/in/muhammadyqb), and Feel free to message me on if you have any queries.

See you with the next review. Hopefully its OSCP🤞