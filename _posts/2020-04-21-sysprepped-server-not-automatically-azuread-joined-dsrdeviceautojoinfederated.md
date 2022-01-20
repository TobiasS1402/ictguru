---
id: 64
title: Sysprepped server not automatically AzureAD joined
date: 2020-04-21T20:04:29+02:00
layout: post
guid: http://new.ictguru.nl/?p=64
permalink: /sysprepped-server-not-automatically-azuread-joined-dsrdeviceautojoinfederated/
image: /wp-content/uploads/2020/04/external-content.duckduckgo.com_.png
categories:
  - Blogposts
  - Microsoft
---
Around the start of the corona crisis a client of ours needed a big upgrade for his RDS farm. The current farm existed of 5 RD servers and 1 gateway. We would sysprep 5 servers for a total of 10 RD servers.

I should mention that those RD Servers are hybrid joined, local and via AzureAD. We configured the 5 servers en went about our day. Until users started complaining about conditional access policies on the RD servers. Five of them were not AzureAD joined and that caused problems.

{% highlight powershell %}
DsrDeviceAutoJoinFederated failed with -2146893802
wmain: failed with error code 0x80090016.
{% endhighlight %}

Manually running the workplace join task (Task scheduler>Microsoft>Windows>Workplace Join>Automatic Device Join) dit not help, it just created these errors. After a long internet search i found the solution.

{% highlight powershell %}
Rename-Item -Path "C:\ProgramData\Microsoft\Crypto\Keys" -NewName "KeysOLD"
New-Item -Path "C:\ProgramData\Microsoft\Crypto\Keys" -ItemType Directory
Get-Acl -Path "C:\ProgramData\Microsoft\Crypto\KeysOLD" | Set-Acl -Path "C:\ProgramData\Microsoft\Crypto\Keys"
{% endhighlight %}