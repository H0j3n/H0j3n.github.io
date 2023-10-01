---
title: Offsec Web Expert OSWE Review
author: H0j3n
date: 2023-10-01 00:00:00
categories: [review]
tags: [certification,certification,oswe]
render_with_liquid: false
---

## Introduction

After completing my OSEP exam in June, I didn't expect to take another Offsec certication so soon. But, my team won the Asia Pacific Cyberattack Response Challenge 2023 and we were awarded Offsec vouchers as prizes. Thanks to [Cyber8lab](https://cyber8lab.com/) and [Offsec](https://www.offsec.com/) for sponsoring these prizes!

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/11_oswe_review/oswe_1.png)

The Offensive Security web Expert (OSWE) certification, formerly known as Advanced Web Attacks and Exploitation (WEB-300), is an advanced web application security course that teaches the skills needed to conduct white box web app penetration tests. If you are pursuing the Offensive Certified Expert (OSCE3), OSWE can be your first or next step in your certification journey.

You can get the full syllabus for OSWE (WEB-300) in [here](https://www.offsec.com/courses/web-300/download/syllabus).

Once you buy the course, you'll receive access to course materials such as PDFs, videos, and labs. In my opinion, I think you should have enough time with the 90-day period to go through all the course materials and complete the labs. 

For more details, Offsec already provide more explanation in [here](https://help.offsec.com/hc/en-us/articles/360046418812-OSWE-Exam-FAQ)

## Labs

Compared to the OSEP, the OSWE labs do not provide any flags for you to read and submit on their platform. But you will be able to hands-on in their labs and review the source code of each of the vulnerable web application.

Personally, I found it quite overwhelming as each of the labs contains thousands lines of code. Therefore, it's important to take your time to understand and exploit the vulnerabilities. The OSWE materials include walkthroughs for each of the labs, so I mainly went through the materials first and then proceeded with the labs.

During the first month, I manage to complete the first 5 modules with their labs and try to create an exploitation scripts for each of the labs. In the final month, I completed all the materials and some of the labs due to the time constraints that I have.

The most important part is to ensure all scripts, notes, references are well-documented on your machine. I'm using Visual Studio Code on my Kali machine to easily go through my exploitation scripts and notes which I can easily grep later using the following structure:

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/11_oswe_review/oswe_2.png)


> CTF - Folder where I put all the scripts I encountered while playing CTF
{: .prompt-info }

> FastCopy - Folder where I will put all the important function that I can easily copy to start my exploitation scripts
{: .prompt-info }

> HTB_Other - Folder where I put all the full chain exploitation scripts for HTB labs or any vulnerable web app I encountered in the internet
{: .prompt-info }

> Labs - Folder where I put all the exploitation scripts for OSWE labs
{: .prompt-info }

But still, [CherryTree](https://www.giuspen.net/cherrytree/) will be the main app for my note taking 😁

![Alt text](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/11_oswe_review/oswe_3.png)

## Exam

I scheduled my exam for Sunday, 24 September at 5 AM (Malaysia time). The main reason why I chose 5 AM because I had just returned from Singapore after attending [Bsides Singapore](https://bsidessg.org/) on Saturday, 23 September. Fortunately, I made it back to Malaysia one day before the exam. I went to bed early, around 9 PM without touching my computer and when I woke up around 3.30 AM, my anxiety spiked because my computer suddently woudln't turn on.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/11_oswe_review/oswe_4.gif)

After do some magic touch, it works for now atleast. I can only hope it will not suddenly turn off during the exam. Before the exam, you need to ensure that you have checked all the necessary requirements before beginning your exam. As usual, all the information can be found on the Offsec website in [here](https://help.offsec.com/hc/en-us/articles/360046869951-WEB-300-Advanced-Web-Attacks-and-Exploitation-OSWE-Exam-Guide).

The exam will last for 48 hours, as mentioned on the Offsec website. However, according to the website, the actual duration is 47 hours and 45 minutes. So mine started at 5 AM (September 24th) and end around 5 AM (September 26th). As stated in one of the [FAQs](https://help.offsec.com/hc/en-us/articles/360046869951-WEB-300-Advanced-Web-Attacks-and-Exploitation-OSWE-Exam-Guide#point-allocation) in Offsec website, you must achieve a minimum scope of **85 points** to pass the exam and you can get maximum of **100 points** available on the exam.

I highly suggest for you to **READ** multiple times the [Exam Restrictions](https://help.offsec.com/hc/en-us/articles/360046869951-WEB-300-Advanced-Web-Attacks-and-Exploitation-OSWE-Exam-Guide#exam-restrictions). Also, to take note that you can't download **ANY** applications, files or source code from the exam environment to your local machine as its strictly forbidden.

I utilize all of the 48 hours to get enough points to pass the exam. The most important thing is to ensure all of the evidence, commands, payloads already in your notes. I put all the steps and evidence in a Microsoft Word and use the [report template](https://www.offsec.com/awae/OSWE-Exam-Report.docx) provided by Offsec as my final report. I did mentioned in my OSEP review, I prefer Microsoft Word because it give you flexibility to customize your report and I love to add page break on every sub headers just to make your report looks nice. I also did refer to this[ OSWE Exam Report Template Markdown](https://github.com/noraj/OSCP-Exam-Report-Template-Markdown/blob/master/src/OSWE-exam-report-template_noraj_v1.md) owned by noraj and I highly recommend to check it out.

During the exam, I also highly recommended for you to use the revert as many times as you can. Especially when you want to test your exploit, please revert it first then run the scripts. This will help you be more confident that your exploit is working fine on the target machine.


## Tips and Recommendation

There are several platforms you can use to find answers to any of your questions before the actual exam. I will list all the platforms I used to gather information prior to the exam.

1. [OffSec FAQs](https://help.offsec.com/hc/en-us/articles/360046418812-OSWE-Exam-FAQ)
2. [Reddit - OSEP](https://www.reddit.com/r/oswe/)
3. [OffSec - Discord](https://discord.com/invite/offsec)
4. [OffSec - Forum](https://forums.offensive-security.com/)

Thanks to the community, there are a lot of cheatsheets that you can refer to prepare for the OSWE.

1. [OSWE Resources #1](https://jorgectf.gitbook.io/awae-oswe-preparation-resources/)
2. [Exploit Writing](https://github.com/rizemon/exploit-writing-for-oswe)
3. [Pentesterlab - CodeReview](https://pentesterlab.com/exercises/codereview/course)
4. [OWASP CheatSheets](https://github.com/OWASP/CheatSheetSeries/blob/master/Index.md)
5. [WhiteBox Pentest](https://github.com/computer-engineer/WhiteboxPentest)
6. [0 Click HTB](https://github.com/brutuspt/0click_HTB/tree/master)
7. [4 Vulnerable Web App](https://github.com/bmdyy?tab=repositories&q=oswe&type=&language=&sort=)
8. [OSWE Resources #2](https://github.com/snoopysecurity/OSWE-Prep)

While doing any OffSec Exam, I always rward myself with a break when I manage to increase my points. Usually I will take a break of atleast 1 hour once I've earned some points. Also, do utilize the debug machine and implement all the knowledge from the course materials with using WhiteBox/BlackBox testing. If you are stuck at something, allocate minimum of 3 hours and maximum of 5 hours to stop and start looking at different places.

If you are looking for different kinds of reverse shell payload to generate in Python3, do check out my [EzpzShell](https://github.com/H0j3n/EzpzShell). I hope it will help you in some ways 😬

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/11_oswe_review/oswe_5.gif)


## What's next?

## Other OSWE Review

1. [OSWE Review & Exam Preparation Guide](https://hub.schellman.com/blog/oswe-review-and-exam-preparation-guide)
2. [InfosecWriteup - OSWE Exam Review and Tips](https://infosecwriteups.com/cert-oswe-exam-review-and-tips-ft-no-developer-background-candidate-1dad7f545155)
3. [OSWE Guide in 2021](https://rayhan0x01.github.io/web/2021/04/12/awae-web-300-oswe-guide-2021.html?i=543)
4. [An Honest OSWE 2023 Review](https://charchitverma100.medium.com/an-honest-oswe-2023-review-my-journey-preparation-and-exam-67d0adcbcde4)
5. [OffSec - OSWE Review](https://www.offsec.com/offsec/offensive-security-awae-oswe-review/)

