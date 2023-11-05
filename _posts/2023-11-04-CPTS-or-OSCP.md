---
layout: post
date: 2023-11-05
title: "CPTS or OSCP"
categories: [hackthebox, Offsec, Offensive Security]
tags: [hackthebox, Offsec]
---

# CPTS or OSCP

## My background

I recently passed CPTS on October 13th 2023 and then I took the OSCP exam on November 4th 2023 and scored 90 points. I had enough points to pass within 3 hours and started studying about a week after I passed the CPTS exam. The only other experience I have is the eJPTv2 certification and about 25 boxes on HackTheBox.


## Cost

The Biggest Difference between the two exams is the cost and the HR recognition that the OSCP receives. You pay 1600$ for 90 days of access for the OSCP and you pay 500$ for one year of access for the CPTS that comes with two exam attempts (one voucher). The HackTheBox pricing structure can be a bit more complicated as they offer a student discount so you can pay 8$ per month and 210$ for an exam voucher. The expected time line for the CPTS is 43 days with a 10 day exam. The OSCP exam took me 2 weeks to accomplish all of the labs and 80% on all exercises for the 10 bonus points and you are expected to take a 24 hour exam and a 24 hour reporting period in order to receive the certificate. On the other hand, I believe the CPTS took me about 2-3 months to complete.


## The Coursework

The two exams are similar in Nature however the CPTS is substantially more difficult and more detailed. They expect you to understand what you are doing, why you are doing it, and what to do if something doesnt work. The OSCP exam was lacking in this department in my opinion as it is known you can do the entire network with very little effort. It is necessary to do manual enumeration and understand what your tools are doing because it is plausible that they will miss something that is vulnerable. 

The Active Directory networks are also not similarly in difficulty. You are expected to be able to perform a full penetration test on your own once you complete the CPTS and I cannot expect the same for the OSCP. The networks from the CPTS challenge seasoned penetration testers substantially where some do fail the exam. The OSCP would not challenge any seasoned penetration tester. 

Some issues that the OSCP exam has is that there are arbritary exam restrictions. I firmly believe that the exam should be built around the tools in order to make them not work due to the challenge that a proper exam would require automated tools not work rather than arbritary requirements. [Exam Guide](https://help.offsec.com/hc/en-us/articles/360040165632-OSCP-Exam-Guide) One example of this is the restriction of SQLMap or meterpreter. The Course as well does not properly you prepare for their labs in my experience. 

The CPTS has its challenges as well such as being long and an extremely long exam, but you do gain a lot from it. The requirement of having to complete the course is not a good thing in my opinion as it limits those who can attempt the certificate quickly because experienced testers will not always need to complete the entire course, but maybe some sections. It is an extremely good thing that you are able to keep the course for life. 

The two courses have some overlap, some material that are not in either and some that in one and not the other. The CPTS covers a lot more and in more detail, but also does not cover everything that is in the OSCP covers.


## Infrastructure

The infrastructure from both courses have many many labs that can be comprehensive. However, they can run into problems due to the configuration. I did not encounter as many issues from the labs going through the CPTS as I did the the OSCP. However I have heard it both ways but mainly towards Offsec having the labs issues. This does create a problem as you are now questioning whether you are having a skill issue or if your lab is broken and is prohibitive to the learning environment. Understanding that you are doing something wrong is a major part of learning in this field and figuring out what you need to learn or change. I did experience this several times during my OSCP jounrey and not too many time during my CPTS experience. Another issue is the proving grounds and how often the boxes reset while you are actively working on the machines. You can be port scanning, looking through the web, looking through an SMB share, etc., and it will reset causing you to lose time and progress and can be extremely frustrating. 


## Reporting

The report template that offsec provides is lacking and not adequate for a proper pentest you would want to deliver to a client. It is incomplete, doesnt show full command outputs, full scripts how to properly secure something etc, You can view the report on their help page which is linked here: [Report](https://help.offsec.com/hc/en-us/articles/360046787731-PEN-200-Reporting-Requirements).

The template HTB provides is a lot to take in at once. It goes into a lot further depth and they provide a full sample report and a template for this report which the OSCP does not do. The HTB report can be found inside their discord or through their course. I believe that Offsec not providing a quality report is an issue in itself as that is 90% of your job as a pentester. Nobody cares if you can hack everything in the world if your report is bad. The issues will not get resolved with a poor report.


## The Staff and Community

The Staff and community are vastly different from HTB and Offsec alike. I find that HTB's community is more willing to help each other out and this creates a better learning environment and enables you to learn far faster. The try harder method from Offsec has its use, but should not always be the first repsonse. The first repsonse should be along the lines of what have you tried and what havent you tried? You should look into this topic or this file etc., however sometimes offsec will link you an entire module and expect you to figure it out or research enough and this is a skill that is necessary. However, it is not good practice during the learning phase in my opinion. Another good thing that HTB offers is their discussions on the forums themselves fostering a beneficial learning environment to those involved. It is also an issue that the OSCP Faq's are very scattered. When I was preparing for my exam, I struggled to find the information necessary due to receving multiple emails about the exam with different information, being told to read the read the FAQ. I believe in total for just the exam, you have close to 10 different links showing the information.


## HR

I do not have the OSCP certifcation just yet. However, it has been around the community that the OSCP will get you a job much quicker than the CPTS will. The CPTS does teach you and it does prepare you better. However, it does not get you that job and I am hoping this changes soon as the CPTS is a more detailed certification experience.


## TLDR; CPTS prepares you better than the OSCP does in almost every way.


[CPTS Course](https://academy.hackthebox.com/preview/certifications/htb-certified-penetration-testing-specialist)
[OSCP](https://offsec.com/courses/pen-200/)
[OSCP Reporting Requirements](https://help.offsec.com/hc/en-us/articles/360046787731-PEN-200-Reporting-Requirements)
[OSCP Exam Guide](https://help.offsec.com/hc/en-us/articles/360040165632-OSCP-Exam-Guide)
