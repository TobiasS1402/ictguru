---
layout: post
author: tobias
title: "Exchange 2016 with 4GB RAM"
date: 2020-04-23T22:01:46+02:00
categories: [Microsoft]
description: "Trying to get Exchange 2016 running with 4GB of RAM"
image: assets/images/exchange.jpg

---
I was doing some exercises for school with exchange server 2016 when I had a fun idea, I was wondering what would happen with 4gb of RAM. The bare minimun is 8GB of ram to get past the prerequisites. On 8GB it ran quite well, so I wondered: what would happen? what **could** happen?

## Testing Exchange 2016 with 4GB RAM

![Hyper-V status for Mail1, It is asking for 10GB of RAM while 4GB of RAM has been assigned.](/assets/images/ram.png)

![Out of memory error](/assets/images/outofmem.png)

Now on to the experiment, I decided to boot the server for the fun with 4GB of ram. It booted just fine, that was until I tried to log into the server. I could not access it via RDP, only the Hyper-V native console. After that I decided to check the server memory. The server was asking about 6GB of non-existant RAM.

Trying to access the OWA portal surpised me, login screen presented itself. When my login finally processed I was disappointed to see the server "officially" ran out of memory.

Okay, back to the drawing board. What if I created a 15 to 20 GB pagefile? 

### Setting up Exchange 2016 with a pagefile

So I created a 20GB pagefile, rebooted the server, waited a long time for it to initialize, and bingo! After the reboot I tried logging into OWA again, and to my surprise I was greeted with the inside of my mailbox. Now lets try to use the actual mailbox, I sent a mail to my own mailbox and waited for the magic to happen. Only for it to never be sent, it completely dissappeared. So I sent another email and decided do investigate using the ECP.

## Conclusion 

To conclude, it is not possible to run Exchange 2016 with 4GB of ram, I don't think that 6GB will change much, 8GB is the bare minimum for a reason. It was a fun experiment. Here are some of the eventlogs:

{% highlight ruby %}
Event ID 7000: The Microsoft Exchange Diagnostics service failed to start due to the following error: The service did not respond to the start or control request in a timely fashion.
Event ID 7009: A timeout was reached (30000 milliseconds) while waiting for the Microsoft Exchange Diagnostics service to connect.
Event ID 2004: Windows successfully diagnosed a low virtual memory condition. The following programs consumed the most virtual memory: w3wp.exe (8528) consumed 455249920 bytes, noderunner.exe (6748) consumed 442880000 bytes, and w3wp.exe (2320) consumed 403537920 bytes.Event ID 1000: Faulting application name: dwm.exe, version: 10.0.14393.0, time stamp: 0x578999ab Faulting module name: dwmcore.dll, version: 10.0.14393.1715, time stamp: 0x59b0d15f Exception code: 0xc00001ad Fault offset: 0x00000000000f5956 Faulting process id: 0xf10 Faulting application start time: 0x01d619b841150774 Faulting application path: C:\Windows\system32\dwm.exe Faulting module path: C:\Windows\system32\dwmcore.dll Report Id: e2e48ab6-8839-4c86-9bd7-c2e956165c3c Faulting package full name: Faulting package-relative application ID:
Event ID 3154: Active Manager failed to mount database EXC-DB01 on server Mail.school.test. Error: An Active Manager operation failed with a transient error. Please retry the operation. Error: Database action failed with transient error. Error: A transient error occurred during a database operation. Error: MapiExceptionNetworkError: Unable to mount database. (hr=0x80040115, ec=-2147221227)`
{% endhighlight %}