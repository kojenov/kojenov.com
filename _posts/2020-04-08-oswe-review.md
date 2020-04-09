---
layout: post
title: OSWE review
image: /assets/2020-04-08-oswe-review/oswe-pass.jpg
summary: Thoughts on AWAE training course and OSWE certification exam
---

Ten days ago, I’ve made my first attempt at [OSWE certification](https://www.offensive-security.com/awae-oswe/#cert), and today I received the official confirmation:

![screenshot](/assets/2020-04-08-oswe-review/oswe-pass.jpg)

I passed!!! Here is my review of the course and the exam.

<!--more-->

## AWAE course

I’ve bought 30 days of [AWAE](https://www.offensive-security.com/awae-oswe/#course) lab access and scheduled it to start on March 15. With ~4 years in web application hacking, plus several years prior to that in application security in general, plus past experience as a software developer, I thought 30 days would be enough for me to go through [the material](https://www.offensive-security.com/documentation/awae-syllabus.pdf).

When I received the course materials and began my studies, I knew immediately my assumptions were correct. As a matter of fact, I completely skipped or skimmed through several chapter right away. Halfway through the course material and the lab, I barely learned anything new. Then it became more interesting, and towards the end it became super interesting when it came to .NET stuff which I had practically no experience with.

I wouldn’t say I’ve learned a lot, but I’ve definitely got reinforcement of prior knowledge, especially on the topics I don’t deal with at work every day. I’ve learned a couple of completely new things, such as PHP type juggling and the entire .NET stuff. I’ve also learned some cool tricks in exploit writing with Python (perhaps, the most surprising part for me was that a blind SQL injection exploit could be easily written from scratch in just a couple of dozen of lines, no need for SQLmap!)

Overall, I think AWAE course is pretty good. The videos are great. It definitely teaches a lot of good stuff. The fact that I didn’t learn a whole lot just means I’ve had a ton of prior experience. I highly recommend doing the Extra Mile exercises, as they teach you how to do research on your own, without being handheld. This skill will come handy both on the exam and in the real life.

On the other hand, AWAE is lacking the OSCP-like lab with a number of systems one could hack on their own. Yes, there are Extra Miles, but I’d like to see more different applications to hack, to get more practice. I’m not asking for dozens of lab machines like OSCP, but just four or five additional apps to hack on one’s own would be great!

I would also highly recommend exploring common application security problems and coding mistakes outside of AWAE such as OWASP Top 10 and similar. The course, as good as it is, does not cover everything. If you work in the appsec, you should already know the common design and coding mistakes you would look for in a code review, but if you don’t, you’ll need to get that knowledge.

## OSWE exam

One can only schedule the exam after starting the AWAE course. When my course started on 3/15, I immediately clicked on the exam scheduling link, and found out that the closest exam date was mid-June! Wow, 3 months out! I had a potential travel in mid-June (which at this point is cancelled due to COVID-19), so I scheduled the exam for June 30. After about a week of studying, I was confident that I could attempt the exam sooner, so I started periodically checking the scheduling page for open slots that randomly pop up due to cancellations and reschedules, and within a couple of days, I saw an opening on 3/28. Well, that was a bit sooner than I wanted (less than 2 weeks through the course!) but I decided to take a chance. I took the rest of the week off work to finish my studies and prepare for the exam, and I’m glad I did.

The exam started at 9am. 15 minutes prior, I connected to the proctoring session and went through the protocol with the proctor. I connected to the exam VPN, shared my screen, moved my camera to show the surroundings, etc. The [proctoring tool](https://support.offensive-security.com/proctoring-tool-student-manual/) runs in the browser, and allows them to monitor your through web camera, as well as to see your entire screen. No sound. I had no issues with the tool in Chrome on my Linux host, but there was a couple of things specific to my environment. First, the screen sharing on Linux sees all monitors as a single [very wide] display, and I had to explain that to the proctor. Second, I was running the proctoring tool on one monitor, and my VirtualBox Kali VM on two other monitors in fullscreen mode. It would seem like I was not running a VM at all, so I launched two terminals and showed different ‘uname -a’ outputs to assure the proctor I was running the proctoring tool on the host and not inside the VM. After I got the OK to proceed with the exam, I began hacking.

First of all, you should carefully read the exam objectives and understand what you are required to do. The objectives are clear but they may differ from one target to another, so make sure you understand them.

Second, document your research and exploitation steps since you would need that documentation for your exam report. I chose to take brief notes and critical screenshots as I was going through the tasks until I succeeded, and then walk through everything again at the end of the exam to gather detailed documentation.

Third, do not look for known vulnerabilities! This is not OSCP!!! In this exam, you are looking for 0days. If you find a vulnerability, do yourself a favor and run a google search to see if there is already a published CVE for that. I found two of these during the exam. These are dead ends that won’t get you any points!

My exam experience was a mix of fun, frustration, tiredness, coffee, excitement, exhaustion, and everything in between. About 7 hours through, I’ve got half of the points and was super excited! However, earning the remaining points took pretty much the rest of the 48 hours. I was looking for a needle in a haystack, wasted a lot of time in rabbit holes, found vulnerabilities which were known CVEs (extremely frustrating!), and didn’t get much sleep.

All of the second day of the exam I had the feeling that I would fail, and I was tempted to give up and schedule a retake. But I decided to move on. Breaks helped me recharge (yes, pleeease, take breaks, don’t kill yourself!), and towards the evening of the second day, I finally had a breakthrough! By 11pm, I’ve got the passing score, and by 2am, I’ve completed 100% of the exam objectives!

I spent the rest of the night walking through the code again, taking screenshots, improving my exploits, taking more screenshots, and drafting the report. I enjoyed a glass of scotch before falling asleep at 7:30am :)

## Is OSWE worth it?

Yes.

If you are a hacker, you need to know how to find and exploit web application vulnerabilities. Sometimes, you’ll have the original or decompiled code available for review. Sometimes, you’ll need to chain several small vulnerabilities to craft a complete kill chain. AWAE is not perfect but it teaches you many of those things.

If you are a hiring manager talking to a candidate with OSWE credentials, know that the person went through a pretty complex learning course and a painful certification exam. Depending on their prior experience, they could have spent many many hours of their spare time on the learning path, but they struggled through and succeeded!
