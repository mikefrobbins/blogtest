---
ID: 13638
post_title: >
  Run 350 Nano Server Virtual Machines on
  a Lenovo ThinkPad P50 with Hyper-V in
  Windows 10
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/03/17/run-350-nano-server-virtual-machines-on-a-lenovo-thinkpad-p50-with-hyper-v-in-windows-10/
published: true
post_date: 2016-03-17 07:30:13
---
If you're a frequent reader of my blog articles on this site, then you're already aware that I recently purchased a <a href="http://shop.lenovo.com/us/en/laptops/thinkpad/p-series/p50/" target="_blank">Lenovo ThinkPad P50</a> with a Xeon CPU, 64 GB of RAM, and upgraded it to SSD hard drives as shown in <a href="http://mikefrobbins.com/2016/03/10/upgrade-to-ssd-hard-drives-in-a-lenovo-thinkpad-p50/" target="_blank">my previous blog article</a>.

I <a href="http://twitter.com/mikefrobbins/status/709549388004130817" target="_blank">tweeted</a> that it wouldn't be complete without a few PowerShell stickers:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-stickers.jpg" rel="attachment wp-att-13641"><img class="alignnone size-full wp-image-13641" src="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-stickers.jpg" alt="p50-stickers" width="800" height="435" /></a>

I received a response from <a href="http://twitter.com/jsnover" target="_blank">Jeffrey Snover</a> wanting to know how many Nano server VM's could be run on it:

<a href="http://twitter.com/jsnover/status/709723657250078720" target="_blank" rel="attachment wp-att-13642"><img class="alignnone wp-image-13642 size-full" src="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-nano1a.jpg" alt="p50-nano1a" width="588" height="105" /></a>

I took that as a challenge and decided to spin up as many Nano server VM's as possible to find out. I added the DSC package to each VM and the storage package to one of them so it could be used as a SMB based DSC pull server. I also added all of them to my mikefrobbins.com test domain and gave each of them a meaningful computer name.

I ran into a few issues along the way that you wouldn't normally run into on a single machine used for a test environment. The network (a Hyper-V internal virtual switch) for my test environment was designed with a class C subnet (254 IPv4 addresses) which clearly wasn't enough IP addresses. That was easy enough to fix though.

The second problem I ran into is that Hyper-V only has a default of 256 MAC addresses so once I reached that threshold, I was unable to boot any more VM's:
<pre class="lang:ps decode:true">Get-VM -Name nano* | Start-VM</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-nano2a.jpg" rel="attachment wp-att-13645"><img class="alignnone size-full wp-image-13645" src="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-nano2a.jpg" alt="p50-nano2a" width="859" height="280" /></a>

This was also easily resolved by changing the defaults in Hyper-V manager:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-nano3a.jpg" rel="attachment wp-att-13646"><img class="alignnone size-full wp-image-13646" src="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-nano3a.jpg" alt="p50-nano3a" width="722" height="241" /></a>

My initial assumption was that I'd run out of disk space before I maxed out the amount of RAM in the machine since each Nano server VM uses an average of 135 MB of RAM when idle:
<pre class="lang:ps decode:true">Get-VM -Name nano* |
Where-Object state -eq running |
Measure-Object -Property MemoryAssigned -Average |
Select-Object -Property Count, @{label='AverageMemoryRAM(MB)';expression={$_.Average / 1MB -as [int]}}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-nano4b.jpg" rel="attachment wp-att-13676"><img class="alignnone size-full wp-image-13676" src="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-nano4b.jpg" alt="p50-nano4b" width="859" height="143" /></a>

What I didn't realize is how small the Nano server footprint is on disk. Each Nano server VM uses an average of 540 MB of disk space:
<pre class="lang:ps decode:true ">Get-ChildItem -Path 'D:\Hyper-V\Virtual Hard Disks\nano*.vhd' |
Measure-Object -Property Length -Average |
Select-Object -Property Count, @{label='AverageSize(MB)';expression={$_.Average / 1MB -as [int]}}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-nano8c.jpg" rel="attachment wp-att-13675"><img class="alignnone size-full wp-image-13675" src="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-nano8c.jpg" alt="p50-nano8c" width="859" height="142" /></a>

I was able to max out the RAM by running 350 Nano Server VM's, 3 Windows Server 2012 R2 (server core) VM's, and a Windows 10 client VM (to manage the server VM's with). The Windows Server 2012 R2 VM's run Active Directory, SQL Server, and one is a Web Server. The total number of VM's running on this one physical laptop computer is 354:
<pre class="lang:ps decode:true">(Get-VM | Where-Object state -eq running).count</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-nano5a.jpg" rel="attachment wp-att-13649"><img class="alignnone size-full wp-image-13649" src="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-nano5a.jpg" alt="p50-nano5a" width="859" height="73" /></a>

Here's a list of the VM's that are running:
<pre class="lang:ps decode:true ">Get-VM | Where-Object state -eq running | Format-Wide -Column 12</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-nano9a.jpg" rel="attachment wp-att-13673"><img class="alignnone size-full wp-image-13673" src="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-nano9a.jpg" alt="p50-nano9a" width="859" height="466" /></a>

How much memory was available with all of those VM's running? 1.5 GB available with 62.4 GB in use:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-nano6a.jpg" rel="attachment wp-att-13651"><img class="alignnone size-full wp-image-13651" src="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-nano6a.jpg" alt="p50-nano6a" width="646" height="593" /></a>

When I tried to push beyond 350 Nano Server VM's, I started receiving the sad face, but only on new VM's that I tried to spin up. This was probably due to the lack of available resources (specifically memory if I had to guess):

<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-nano7a.jpg" rel="attachment wp-att-13655"><img class="alignnone size-full wp-image-13655" src="http://mikefrobbins.com/wp-content/uploads/2016/03/p50-nano7a.jpg" alt="p50-nano7a" width="793" height="360" /></a>

Believe it or not, the base machine and VM's were very responsive even with all of those resources in use. I actually wrote this blog article on the base machine while all of those VM's were running in the background and honestly didn't see any performance problems.

The testing in this blog article was performed using Windows 10 Enterprise Edition (1511) with Hyper-V and the 350 VM's run Windows Server 2016 Technical Preview 4 using the Nano Server installation option.

µ