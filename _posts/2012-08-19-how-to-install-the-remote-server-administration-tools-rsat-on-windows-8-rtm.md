---
ID: 5232
post_title: >
  How to Install the Remote Server
  Administration Tools (RSAT) on Windows 8
  RTM
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/08/19/how-to-install-the-remote-server-administration-tools-rsat-on-windows-8-rtm/
published: true
post_date: 2012-08-19 07:30:40
---
There's good news and bad news. The bad news is based on what I've read on the Internet, the Remote Server Administration Tools for Windows 8 RTM won't be available until next month, although that makes sense because Windows Server 2012 won't be available until next month either. So what do I use in the mean time?

The good news is that it's possible to install the RSAT for Windows 8 Release Preview on Windows 8 RTM if you can't wait. Say what? I already tried that and got an error. There's a certain way you have to do the installation and like I said in my previous blog, if you work in the technology sector and you're not on twitter, you're missing out big time. On Friday August 17th, Brian Wilhite (<a href="http://twitter.com/bwilhite1979" target="_blank">@bwilhite1979</a>) tweeted how to install them.

I've tested this out and it does indeed work. First, read the disclaimer on the right side of this blog since this is unsupported, then launch an elevated command prompt. See <a href="http://mikefrobbins.com/2012/08/18/how-to-activate-windows-8-rtm-enterprise-edition/" target="_blank">my blog from yesterday</a> if you don't know how to do that. <a href="http://www.microsoft.com/en-us/download/details.aspx?id=28972" target="_blank">Download the RSAT for Windows 8 RP</a> if you haven't done so already and extract them into a folder using the following command:
<pre class="lang:batch decode:true">expand -F:* Windows6.2-KB2693643-x64.msu C:\tmp\RSAT</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-rp-aduc-on-rtm1.jpg"><img class="alignnone size-full wp-image-5233" title="win8-rp-aduc-on-rtm1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-rp-aduc-on-rtm1.jpg" width="640" height="250" /></a>

Install the tools using the following command and give it a while. It may not appear to be doing anything, but it will install.
<pre class="lang:batch decode:true crayon-selected">pkgmgr /n:Windows6.2-KB2693643-x64.XML</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-rp-aduc-on-rtm2.jpg"><img class="alignnone size-full wp-image-5234" title="win8-rp-aduc-on-rtm2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-rp-aduc-on-rtm2.jpg" width="640" height="72" /></a>

µ