---
layout: page
title: Presentations
---

1. [OWASP Orange County Meetup May 2020](#owaspocmay2020)
1. [OWASP PDX Meetup May 2020](#owasppdxmay2020)
1. [BSides SF 2020](#bsidessf2020)
1. [AppSec Cali 2020](#appseccali2020)
1. [HushCon Seattle 2019](#hushcon2019)
1. [BSides PDX 2019](#bsidespdx2019)
1. [Global AppSec AMS 2019](#globalappsec2019)
1. [BSides PDX 2018](#bsidespdx2018)
1. [AppSec USA 2018](#appsecusa2018)
1. [OWASP PDX Chapter Meeting May 2018](#owasppdxmay2018)
1. [Portland Java User Group May 2018](#pjugmay2018)
1. [OWASP PDX Chapter Meeting April 2018](#owasppdxapril2018)
1. [BSides PDX 2017](#bsidespdx2017)
1. [Portland ISSA Meeting September 2017](#issaseptember2017)
1. [OWASP PDX Chapter Meeting June 2017](#owasppdxjune2017)

---

### <a name="owaspocmay2020"></a> OWASP Orange County Meetup May 2020

#### Yes, you too can break crypto: Exploiting common crypto mistakes

*28 May 2020*

<!--[announcement](https://www.meetup.com/OWASP-OC/events/270500510/)-->
[recording](https://www.twitch.tv/videos/635070437)

Cryptography is tricky. Sure, everybody knows not to roll out their own crypto, but is it enough? Are the standard algorithms, libraries, and utilities always used the right way? This is of course a rhetorical question! Humans keep making mistakes that other humans can exploit, and Murphy’s law continues to prove true: “If there is a wrong way to do something, then someone will do it.”

In this talk, not only will we discuss what can go wrong, but also how attackers could take advantage of that. Insufficient entropy? Static initialization vector? Key reuse in stream cipher? Lack of ciphertext integrity? We’ve heard these terms and may be familiar with them in theory, but let’s see actual examples of these and other crypto mistakes and corresponding exploits, and understand how they could lead to real life problems.

Are you not on a red team and not interested in exploitation? Then this talk is for you too! Come and learn how to avoid common crypto mistakes in your code!

---

### <a name="owasppdxmay2020"></a> OWASP PDX Meetup May 2020

#### Yes, you too can break crypto: Exploiting common crypto mistakes

*21 May 2020*

<!--[announcement](https://www.meetup.com/OWASP-Portland-Chapter/events/270404725/)-->

Cryptography is tricky. Sure, everybody knows not to roll out their own crypto, but is it enough? Are the standard algorithms, libraries, and utilities always used the right way? This is of course a rhetorical question! Humans keep making mistakes that other humans can exploit, and Murphy’s law continues to prove true: "If there is a wrong way to do something, then someone will do it."

In this talk, not only will we discuss what can go wrong, but also how attackers could take advantage of that. Insufficient entropy? Static initialization vector? Key reuse in stream cipher? Lack of ciphertext integrity? We’ve heard these terms and may be familiar with them in theory, but let’s see actual examples of these and other crypto mistakes and corresponding exploits, and understand how they could lead to real life problems.

Are you not on an offensive team and not interested in exploitation? Then this talk is for you too! Come and learn how to avoid common crypto mistakes in your code!

---

### <a name="bsidessf2020"></a> BSides SF 2020

#### Break crypto like a pro!

*23 February 2020*

<!--[announcement](https://sched.co/Ybgu)-->
[recording](https://youtu.be/lRk-YWBHoNg)

Cryptography is hard. Doing it right is even harder, and Murphy’s law continues to prove true: “If there is a wrong way to do something, then someone will do it.” Come learn how to exploit common crypto mistakes in theory and in practice! 

---

### <a name="appseccali2020"></a> AppSec Cali 2020

#### [in]secure deserialization, and how [not] to do it

*23 January 2020*

<!--[announcement](https://sched.co/XLtq)-->
[recording](https://youtu.be/IE8o58XT9Co)

*Yes, the same talk presented the fourth time :)*

---

### <a name="hushcon2019"></a> HushCon Seattle 2019

#### [in]secure deserialization, and how [not] to do it

*6 December 2019*

<!--[announcement](http://hushcon.com/schedule.html)-->

*Yes, the same talk presented the third time :)*

---


### <a name="bsidespdx2019"></a> BSides PDX 2019

#### [in]secure deserialization, and how [not] to do it

*25 October 2019*

<!--[announcement](https://bsidespdx.org/events/2019/speakers.html#[In]secure%20deserialization,%20and%20how%20[not]%20to%20do%20it)-->
[recording](https://youtu.be/xPV5dA1Nvck)

Serialized data is neither new nor exciting. Serialization and deserialization have been in use by countless applications, services and frameworks for a long time. Many programming languages support serialization natively, and most people seem to understand it well. However, many of us don’t fully understand security implications of data deserialization, and in the last couple of years this topic got an increasing focus in the security community, up to the point that insecure deserialization made it to the list of OWASP Top 10 most critical web application security risks! Needless to say high-severity vulnerabilities in some well-known applications as well as popular frameworks such as Apache Struts and Apache Commons Collections raised awareness of this risk.

In this session, we’ll discuss how serialized data are used in software, talk about different serialization formats and the dangers of deserializing untrusted input. We will review some real life vulnerabilities and related exploits. The presentation will contain several code examples with live demos of bypassing security controls by exploiting deserialization vulnerabilities. We’ll forge a session cookie, elevate privileges, cause a denial of service, and even perform a remote code execution - all via insecure deserialization! The demos will use native Java, Python and .NET serialization, as well as JSON and XML formats. Of course, we’ll also talk about how to deserialize in secure way!

Next time you develop your awesome web or mobile app or a microservice, keep in mind how a clever attacker could create and supply malicious data to your application, and thinking like a hacker you could write more secure code!

---

### <a name="globalappsec2019"></a> Global AppSec AMS 2019

#### [in]secure deserialization, and how [not] to do it

*27 September 2019*

<!--[announcement](https://sched.co/TeoZ)-->
[recording](https://youtu.be/Y0QxwRyqlh8)

Serialized data is neither new nor exciting. Serialization and deserialization have been in use by countless applications, services and frameworks for a long time. Many programming languages support serialization natively, and most people seem to understand it well. However, many of us don’t fully understand security implications of data deserialization, and in the last couple of years this topic got an increasing focus in the security community, up to the point that insecure deserialization made it to the list of OWASP Top 10 most critical web application security risks! Needless to say high-severity vulnerabilities in some well-known applications as well as popular frameworks such as Apache Struts and Apache Commons Collections raised awareness of this risk. In this session, we’ll discuss how serialized data are used in software, talk about different serialization formats and the dangers of deserializing untrusted input. We will review some real life vulnerabilities and related exploits. The presentation will contain lots of code examples with live demos of bypassing security controls by exploiting deserialization vulnerabilities. We’ll forge a session cookie, elevate privileges, alter execution flow, and even perform a remote code execution - all via insecure deserialization! The demos will use native Java and .NET serialization, as well as JSON, XML, and other formats. Of course, we’ll also talk about how to deserialize in secure way! Next time you develop your awesome web or mobile app or a microservice, keep in mind how a clever attacker could create and supply malicious data to your application, and thinking like a hacker you could write more secure code! 

---

### <a name="bsidespdx2018"></a> BSides PDX 2018

#### Reverse engineering CISSP practice exams (no cheating!)

*26 October 2018*

<!--[announcement](https://bsidespdx.org/events/2018/speakers.html#kojenov)-->
[recording](https://youtu.be/TPIiaRKqeo0)

If you read this catchy title and thought “Finally, somebody is going to teach me how to easily pass the dreaded CISSP exam!”, you’d be disappointed. This talk is not about cheating a professional exam (who would ever do that?) As a matter of fact, this talk is not much about CISSP at all. Instead, we’ll be talking about reverse engineering a Java program and reusing its code to extract the valuable data, or in other words, using a few hacking techniques against an imperfect application to improve user experience, get the most out of the application, and ultimately, save one’s precious time. I will demonstrate in real time how to crack open an executable, decompile the underlying Java code, understand its behavior, and reuse the existing classes to make them do exactly what you want. While this is not rocket science, you should come to this talk having some basic Java programming knowledge, or at least being able to read and understand Java code. And in case you were wondering, I did pass the exam and got my CISSP certification, and I’m pretty sure this exercise contributed to my success.

---

### <a name="appsecusa2018"></a> AppSec USA 2018

#### Deserialization: what, how and why [not]

*12 October 2018*

<!--[announcement](https://sched.co/F04J)-->
[recording](https://youtu.be/t-zVC-CxYjw)

Insecure deserialization was recently added to OWASP's list of the top 10 most critical web application security risks, yet it is by no means a new vulnerability category. For years, data serialization and deserialization have been used in applications, services and frameworks, with many programming languages supporting them natively. Deserialization got more attention recently as a potential vehicle to conduct several types of attacks: data tampering, authentication bypass, privilege escalation, various injections and, ultimately, remote code execution. Two prominent vulnerabilities in Apache Commons and Apache Struts, both allowing remote code execution, also contributed to raising awareness of this risk.

We will discuss how data serialization and deserialization are used in software, the dangers of deserializing untrusted input, and how to avoid insecure deserialization vulnerabilities. The presentation will contain several code examples with live demos of bypassing security controls due to incorrect deserialization. The examples and demos will use Java and its native serialization, but the techniques can be extrapolated to other languages and formats. 

---

### <a name="owasppdxmay2018"></a> OWASP PDX Chapter Meeting May 2018

#### Pen testing: How to get bigger bang for your buck

*22 May 2018*

<!--[announcement](https://calagator.org/events/1250473728)-->

Panel discussion - Join local industry practitioners as they discuss the best practices used in getting superior results from your Pen Testing. Also share your ideas on Dos and Dont's of Pen testing.

Moderator: Brian Ventura

Panelists: Alexei Kojenov, Ian Melven, Benny Zhao, and Scott Cutler 

---

### <a name="pjugmay2018"></a> Portland Java User Group May 2018

#### How to protect against deserialization attacks

*15 May 2018*

<!--[announcement](https://www.meetup.com/PDXJUG/events/250603125/)-->

Insecure deserialization is one of the most critical web application security risks, yet it is by no means a new vulnerability category. Data serialization and deserialization have been used widely in applications, services and frameworks, with many programming languages supporting them natively. Deserialization got more attention recently as a potential vehicle to conduct several types of attacks: data tampering, authentication bypass, privilege escalation, various injections and, finally, remote code execution. Two recent vulnerabilities in Apache Commons and Apache Struts, both allowing remote code execution, helped raise awareness of this risk.

We will discuss how data serialization and deserialization are used in software, the dangers of deserializing untrusted input, and how to avoid insecure deserialization vulnerabilities.

---

### <a name="owasppdxapril2018"></a> OWASP PDX Chapter Meeting April 2018

#### Deserialization attacks

*16 April 2018*

<!--[announcement](https://calagator.org/events/1250473360)-->

Insecure deserialization was recently added to OWASP's list of the top 10 most critical web application security risks, yet it is by no means a new vulnerability category. Data serialization and deserialization have been used widely in applications, services and frameworks, with many programming languages supporting them natively. Deserialization got more attention recently as a potential vehicle to conduct several types of attacks: data tampering, authentication bypass, privilege escalation, various injections and, finally, remote code execution. Two recent vulnerabilities in Apache Commons and Apache Struts, both allowing remote code execution, helped raise awareness of this risk.

We will discuss how data serialization and deserialization are used in software, the dangers of deserializing untrusted input, and how to avoid insecure deserialization vulnerabilities.

---

### <a name="bsidespdx2017"></a> BSides PDX 2017

#### Hacking a hackathon for fun and profit

*21 October 2017*

<!--[announcement](https://bsidespdx.org/events/2017/speakers.html#AlexeiKojenovAlexIvkin)-->
[recording](https://youtu.be/ZWOXkKfflcE)

All modern software, but the most trivial one, relies on common libraries to perform routine work. Your software may be bastion of security, exhaustively tested and evaluated, but once a vulnerability is discovered in a library you depend on, all bets are off. These large and pervasive vulnerabilities quickly become popular targets, exploited by everybody from script kiddies, to professional hackers, to state actors. It is no surprise that the use of vulnerable libraries is included in the OWASP Top 10 list. The Australian Signals Directorate (ASD) lists patching operating systems and applications as two of their top four strategies to mitigate security incidents!

During a recent hacking game, we’ve identified and exploited a vulnerability not anticipated by the developers. One little crack in a widely used library gave us the footing we needed to construct an attack chain of remote code execution, file upload, data exfiltration, source code disassembly, and branching into a private network, all despite extremely high level of hardening on the target from unintended attacks. We’ll share with you how a safe and fun library exploitation can be in the confines of a hacking game, and how there are serious implications for your corporate applications where the stakes are much higher. In fact, the very same vulnerability we exploited to own the hackathon was used by criminals to breach Equifax!

---

### <a name="issaseptember2017"></a> Portland ISSA Meeting September 2017

#### Hacking a hackathon

*14 September 2017*

<!--[announcement](http://portland.issa.org/2017/past-presentations/september-lunch-meeting-hacking-a-hackathon/)-->

All modern software, but the most trivial one, relies on common libraries to perform routine work. Your software may be bastion of security, exhaustively tested and evaluated, but once a vulnerability is discovered in a library you depend on, all bets are off. These large and pervasive vulnerabilities quickly become popular targets, exploited by everybody from script kiddies, to professional hackers, to state actors. It is no surprise that the use of vulnerable libraries is included in the OWASP Top 10 list. The Australian Signals Directorate (ASD) lists patching operating systems and applications as two of their top four strategies to mitigate security incidents!

During a recent hacking game, we’ve identified and exploited a vulnerability not anticipated by the developers. One little crack in a widely used library gave us the footing we needed to construct an attack chain of remote code execution, file upload, data exfil, source code disassembly, and branching into a private network, all despite extremely high level of hardening on the target from unintended attacks. We’ll share with you how a safe and fun library exploitation can be in the confines of a hacking game, and how there are serious implications for your corporate applications where the stakes are much higher.

---

### <a name="owasppdxjune2017"></a> OWASP PDX Chapter Meeting June 2017

#### Cheating a hacking game for fun and profit

*19 June 2017*

<!--[announcement](https://calagator.org/events/1250471956)-->

All modern software, but the most trivial one, relies on common libraries to perform routine work. Your software may be bastion of security, exhaustively tested and evaluated, but once a vulnerability is discovered in a library you depend on, all bets are off. These large and pervasive vulnerabilities quickly become popular targets, exploited by everybody from script kiddies, to professional hackers, to state actors. It is no surprise that the use of vulnerable libraries is included in the OWASP Top 10 list. The Australian Signals Directorate (ASD) lists patching operating systems and applications as two of their top four strategies to mitigate security incidents!

During a recent hacking game, we've identified and exploited a vulnerability not anticipated by the developers. One little crack in a widely used library gave us the footing we needed to construct an attack chain of remote code execution, file upload, data exfil, source code disassembly, and branching into a private network, all despite extremely high level of hardening on the target from unintended attacks. We'll share with you how a safe and fun library exploitation can be in the confines of a hacking game, and how there are serious implications for your corporate applications where the stakes are much higher.


