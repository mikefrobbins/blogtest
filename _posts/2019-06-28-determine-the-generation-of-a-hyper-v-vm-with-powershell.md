---
ID: 17922
post_title: >
  Determine the Generation of a Hyper-V VM
  with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/06/28/determine-the-generation-of-a-hyper-v-vm-with-powershell/
published: true
post_date: 2019-06-28 07:30:00
---
Ever need to determine what generation all of the virtual machines are on your Hyper-V servers? This is simple using the <em>Get-VM</em> command that installs as part of the Hyper-V module for Windows PowerShell.
<pre class="lang:ps decode:true">Get-WindowsOptionalFeature -FeatureName *hyper*powershell -Online</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/06/vm-generation1a.jpg"><img class="alignnone size-full wp-image-17923" src="https://mikefrobbins.com/wp-content/uploads/2019/06/vm-generation1a.jpg" alt="" width="859" height="230" /></a>

While the previous command will work on both clients and servers, the following command could also be used on a Windows server.
<pre class="lang:ps decode:true ">Get-WindowsFeature -Name *hyper*powershell</pre>
The generation of the VM is one of the properties from the results of <em>Get-VM</em>.
<pre class="lang:ps decode:true">Get-VM | Select-Object -Property Name, Generation</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/06/vm-generation2a.jpg"><img class="alignnone size-full wp-image-17924" src="https://mikefrobbins.com/wp-content/uploads/2019/06/vm-generation2a.jpg" alt="" width="859" height="301" /></a>

The previous example is a list of all the VM's on my local Windows 10 system that has the Hyper-V role installed. This same command will work on Hyper-V servers and it can always be wrapped inside of <em>Invoke-Command</em> to have it run against numerous remote Hyper-V servers in parallel.
<pre class="lang:ps decode:true ">Invoke-Command -ComputerName HyperV-Server01, HyperV-Server02, HyperV-Server03 {
    Get-VM | Select-Object -Property Name, Generation
} | Select-Object -Property Name, Generation</pre>
Thoughts, questions, comments? Please post them as a comment to this blog article.

µ