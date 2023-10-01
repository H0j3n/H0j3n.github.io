---
title: Offsec Web Expert OSWE Review
author: H0j3n
date: 2023-10-01 00:00:00
categories: [review]
tags: [certification,certification,oswe]
render_with_liquid: false
---

## Introduction

After completing my OSEP exam in June, I didn't expect to take another Offsec certication so soon. But, my team won one the Asia Pacific Cyberattack Response Challenge 2023 and we were awarded Offsec vouchers as prizes. Thanks to [Cyber8lab](https://cyber8lab.com/) and [Offsec](https://www.offsec.com/) for sponsoring these prizes!

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/10_osep_review/osep_1.png)

The Offensive Security web Expert (OSWE) certification, formerly known as Advanced Web Attacks and Exploitation (WEB-300), is an advanced web application security course that teaches the skills needed to conduct white box web app penetration tests. If you are pursuing the Offensive Certified Expert (OSCE3), OSWE can be your first or next step in your certification journey.

You can get the full syllabus for OSWE (WEB-300) in [here](https://www.offsec.com/courses/web-300/download/syllabus).

Once you buy the course, you'll receive access to course materials such as PDFs, videos, and labs. In my opinion, I think you should have enough time with the 90-day period to go through all the course materials and complete the labs. 

For more details, Offsec already provide more explanation in [here](https://help.offsec.com/hc/en-us/articles/360046418812-OSWE-Exam-FAQ)

## Labs

What I love most about the Offsec courses, including OSEP, is the good lab experience they provide. Just like my time with OSCP, I had a great time working on the labs in OSEP. The hands-on of the labs really enhances the learning process and lets you put your skills into action. It's something that I genuinely enjoy and find extremely valuable in the course.

The labs in OSEP are divided into six challenges. Some of these labs concentrate on specific topics, giving you practice in those areas. Meanwhile, there are labs that simulate smaller internal networks, where your goal is to compromise them. These internal networks you can expect to have security mechanisms, Active Directory environments, and lateral movement.

During the first month, my main focus is to finish reading the course materials (PDFs and videos). In the second and third months, I dedicate my time to repeatedly solving all six challenges, using different payloads, tools, or techniques. I highly recommend taking notes of all commands or payloads in your notebook, as they can be useful references during the exam.

If you are asking me about note taking apps, I really love using [CherryTree](https://www.giuspen.net/cherrytree/) now 😁

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/10_osep_review/osep_2.png)

## Exam

Initially, I had scheduled my exam for June 6th, but I decided to reschedule it to June 5th, one day earlier. The main reason for this change is that I have a lot of free time during the weekend, and Monday happens to be a public holiday in Malaysia. Waiting for the exam day can make the days feel longer, and I wanted to make the most of my available time.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/10_osep_review/osep_3.png)

Before the exam, you need to ensure that you have checked all the necessary requirements before beginning your exam. As usual, all the information can be found on the Offsec website in [here](https://help.offsec.com/hc/en-us/articles/360050293792-OSEP-Exam-Guide).

The exam will last for 48 hours, as mentioned on the Offsec website. However, according to the website, the actual duration is 47 hours and 45 minutes. So mine started at 2 PM (June 5th) and end around 2 PM (June 7th). As stated in one of the [FAQs](https://help.offsec.com/hc/en-us/articles/360049781352-OSEP-Exam-FAQ#h_01FSRPPQMECEQTW1M4TGTQQAB2) in Offsec website, there are 2 ways you can pass the exam, either you achieve the objective provided on the control panel (secret.txt) or obtain at least 100 points. 

> 1 Flag = 10 Points. So 10 Flags = 100 Points. Can read the FAQs in [here](https://help.offsec.com/hc/en-us/articles/360049781352-OSEP-Exam-FAQ#h_01FSRPN7N18ZYS8Z5B8X3R6J51)
{: .prompt-info }

I utilize all of the 48 hours to get enough points to pass the exam. The most important thing is to ensure all of the evidence, commands, payloads already in your notes. I put all the steps and evidence in a Microsoft Word and use the [report template](https://www.offensive-security.com/osep-online/OSEP-Exam-Report.docx) provided by Offsec as my final report. The reason why I prefer Microsoft Word because it give you flexibility to customize your report and I love to add page break on every sub headers just to make your report looks nice. I also did refer to this [OSEP Exam Report Templatee Markdown](https://github.com/noraj/OSCP-Exam-Report-Template-Markdown/blob/master/src/OSEP-exam-report-template_ceso_v1.md) owned by [noraj](https://github.com/noraj) and I highly recommend to check it out.

## Tips and Recommendation

There are several platforms you can use to find answers to any of your questions before the actual exam. I will list all the platforms I used to gather information prior to the exam.

1. [OffSec FAQs](https://help.offsec.com/hc/en-us/articles/360049781352-OSEP-Exam-FAQ)
2. [Reddit - OSEP](https://www.reddit.com/r/osep/)
3. [OffSec - Discord](https://discord.com/invite/offsec)
4. [OffSec - Forum](https://forums.offensive-security.com/)

Thanks to the community, there are a lot of cheatsheets that you can refer to prepare for the OSEP.

1. [OSEP Code Snippets](https://github.com/chvancooten/OSEP-Code-Snippets)
2. [OSEP Notes #1](https://github.com/In3x0rabl3/OSEP)
3. [OSEP Notes #2](https://github.com/timip/OSEP)
4. [OSEP Notes #3](https://github.com/gh0x0st/OSEP-Breaking-Chains)
5. [Mayfly - Game Of Active Directory v2](https://mayfly277.github.io/posts/GOADv2/)
6. [MindMap - Pentesting Active Directory](https://orange-cyberdefense.github.io/ocd-mindmaps/img/pentest_ad_dark_2022_11.svg)
7. [CheatSheets - Active Directory](https://hideandsec.sh/books/cheatsheets-82c/page/active-directory)
8. [HackTricks - Linux Active Directory](https://book.hacktricks.xyz/linux-hardening/privilege-escalation/linux-active-directory)
9. [HackTricks -  Active Directory Methodology](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology)
10. [Windows & Active Directory Exploitation Cheat Sheet](https://casvancooten.com/posts/2020/11/windows-active-directory-exploitation-cheat-sheet-and-command-reference/)

Share EzpzShell


## What's next?

After sending the report on the same day of my last day (June 7th), I keep refreshing my email since then.

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/10_osep_review/osep_5.png)

On the night of June 8th, I finally received the email saying that I passed the exam. This certification has always been something I wanted, and I never thought I would actually get it. I know I still have a lot to learn and I'm not an expert, but I enjoy learning and that's what matters. Now that I have OSEP, I'm thinking of going for the OSCE3 certification next. The OSWE will be the next certification I want to pursue!

![](https://raw.githubusercontent.com/H0j3n/H0j3n.github.io/master/assets/img/uploads/10_osep_review/osep_6.png)

## Other OSEP Review

- [OSEP Tester PEN-300 Course Review](https://www.schellman.com/blog/cybersecurity/osep-and-pen-300-course-review)
- [Offensive Security Experienced Penetration Tester (OSEP) Review and Exam](https://spaceraccoon.dev/offensive-security-experienced-penetration-tester-osep-review-and-exam/)
- [OSEP COURSE REVIEW](https://makosecblog.com/miscellaneous/osep-course-review/)
- [Offensive Security OSEP Review](https://www.hopinfosec.com/evasion/2021/07/08/OSEP-Review/)
- [Offensive Security Experienced Penetration Tester (OSEP) Review](https://steflan-security.com/offensive-security-experienced-penetration-tester-osep-review/)