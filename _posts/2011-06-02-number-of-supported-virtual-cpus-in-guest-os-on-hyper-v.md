---
ID: 2331
post_title: 'Number of Supported Virtual CPU&#8217;s for a Guest on Hyper-V'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/06/02/number-of-supported-virtual-cpus-in-guest-os-on-hyper-v/
published: true
post_date: 2011-06-02 07:30:13
---
Question:
How many virtual processors (virtual CPU's) are officially supported by Microsoft for a guest virtual machine (VM) running on Hyper-V?

Answer:
You may be thinking the answer to this question is four since that is the number you can assign to any guest VM, but the answer like many others in IT is that it depends. It depends on what guest operating system you're running. Windows Server 2003, Windows Vista, and Windows XP SP3 all officially support a maximum of two virtualized processors when installed as a guest VM running on Hyper-V. This is according to the Microsoft Windows Server 2008 R2 website: "<a href="http://www.microsoft.com/windowsserver2008/en/us/hyperv-supported-guest-os.aspx" target="_blank">Virtualization with Hyper-V: Supported Guest Operating Systems</a>" and Microsoft TechNet: "<a href="http://technet.microsoft.com/en-us/library/cc794868(WS.10).aspx" target="_blank">About Virtual Machines and Guest Operating Systems</a>". Both of these sites also list the maximum number of virtual CPU's for all of the other supported guest operating systems such as Windows 2000 SP4 only supporting a maximum of one virtualized processor. This is something to keep in mind when virtualizing older operating systems as guest VM's on Hyper-V.

µ