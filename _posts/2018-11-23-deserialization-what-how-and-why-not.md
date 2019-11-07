---
layout: post
title: "Deserialization: what, how and why [not]"
---

Insecure deserialization was recently added to OWASP's list of the [top 10 most critical web application security risks](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), yet it is by no means a new vulnerability category. For years, data serialization and deserialization have been used in applications, services and frameworks, with many programming languages supporting them natively. Deserialization got more attention recently as a potential vehicle to conduct several types of attacks: data tampering, authentication bypass, privilege escalation, various injections and, ultimately, remote code execution. Two prominent vulnerabilities in Apache Commons and Apache Struts, both allowing remote code execution, also contributed to raising awareness of this risk.

There was a storm of talks and publications on the subject back in 2015-2016, but relatively few recently. While deserialization is a well researched class of vulnerabilities, and there is a lot of information out there, many non-security folks still don't understand it well enough, which leads to new vulnerabilities in the code. We need to continually educate on the subject. I presented on this topic three times this year: at [OWASP Portland](https://calagator.org/events/1250473360), at [Portland Java User Group](https://www.meetup.com/PDXJUG/events/250603125/), and at [AppSec USA 2018](https://appsecus2018.sched.com/event/F04J/deserialization-what-how-and-why-not).

<!--more-->

Feel free to use my [code examples](https://github.com/kojenov/serial) and [slides](https://drive.google.com/open?id=1o8VPE4nwNLb9cAYfG-3gM4WR4CY7FwoT) to present at your OWASP chapter meetings, workplaces, or anywhere else you see fit!

<iframe width="560" height="315" src="https://www.youtube.com/embed/t-zVC-CxYjw" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

By the way, I was not the only one [talking about deserialization at AppSec USA 2018](https://appsecus2018.sched.com/event/F03D/better-deserialization-vulnerability-remediation-with-automated-gadget-chain-discovery), w00t!
