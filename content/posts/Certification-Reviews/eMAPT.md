---
author: "0xt0pus"
title: "eMAPT Exam Review"
date: "2024-06-02"
description: "eLearn Security Mobile Application Penetration Tester (eMAPT) is a mobile application penetration testing certification by INE (formerly known as eLearnSecurity)."
tags: ["eMAPT", "Penetration Testing", "Certification","Mobile Application"]
cover:
    image: "/certification/emapt/eMAPT.png"
ShowToc: true
TocOpen: false
---

# What is eMAPT?

eLearn Mobile Application Penetration Tester (eMAPT) is a Mobile Application Penetration Testing certification by INE (formerly known as eLearnSecurity). This exam proves that certified professionals have adequate knowledge to perform Penetration Testing of mobile applications (Android and IOS) and can provide exploit application. 

| Price              | 400$ (Without training)                                                  |
| ------------------ | ------------------------------------------------------------------------ |
| Training Cost      | 749$                                                                     |
| Passing score      | (Working Android Application as POC)                                     |
| Allowed time       | 7 days                                                                   |
| Proctored?         | No                                                                       |
| Link to purchase   | [Here](https://security.ine.com/certifications/emapt-certification/)     |
| Exam Focused Areas | Android Application Penetration Testing, Android Application Development |


# My Experience:

I started the exam in the morning of 18th May 2024, uploaded the report on 20th May 2024 and received the result on 29th May 2024. 

I prepared for this exam with the INE official course which is designed for mobile application penetration testing (Link is [here](https://my.ine.com/CyberSecurity/learning-paths/eec5479e-a8d1-4803-817f-c016bb528639/mobile-application-penetration-testing-professional)). This course covers everything needed to pass the exam. But you may not find it as much interactive as you can expect, as it has lengthy slides to cover most of the topics. The course has the following two sections. 
1. Android & Mobile App Pen testing
2. iOS & Mobile App Pen testing

I also went through the [TCM Security Mobile Application course](https://academy.tcm-sec.com/p/mobile-application-penetration-testing) for the preparation of this exam. 

# Exam Info 

Once you start the exam, you will be given with two android mobile applications with the letter of engagement.  This engagement letter outlines the objectives you need to meet to pass the exam. The requirement to pass the exam is to provide a working android mobile application which can exploit both the given applications. 

# Timeline
I started the exam on 18th May, and I was able to identify the vulnerabilities in the first hour. I spent the first day on analysing the working of both the applications and exploiting these through CLI. The second day I started learning android studio and built a few test android applications. The next day, I built my exploit application which was able to exploit both the given application to perform the required tasks. I tested the built application on different emulators with different API versions as well as on my own mobile phone.  

# Exam Review 

The exam was not that much difficult as I was expecting. It was all about understanding the working of the application by reverse engineering it, retrieving data from the application using the provided functionalities and reconstructing another application by reversing the process. By reversing I mean, in some phases you will encounter different situation e.g. encryption, where you will have to reverse the same process to decrypt the given data.  In the exam, you will not be asked to get a flag or to root any system, in fact you will get the answers in front of you within a few minutes. The requirement to pass the exam is to provide an application which can exploit those vulnerabilities automatically. 

# Tips for the Exam:

- Test the applications on at least one rooted and one non rooted emulator and observe the applications. 
- Understand how Encryption and Hashing can be performed in android applications. 
- Understand Content Providers in android and how these can be exploited. 
- Learn how to build mobile applications with android studio. 
- Learn to analyse the AndroidMenifest.xml file. 


I wish you the best of luck on your mobile application penetration testing journey. Feel free to message me on [LinkedIn](https://linkedin.com/in/muhammadyqb) if you have any queries.
