---
ID: 12105
post_title: >
  Use caution when updating to Windows 10
  RTM with data deduplication enabled
  volumes
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/07/30/use-caution-when-updating-to-windows-10-rtm-with-data-deduplication-enabled-volumes/
published: true
post_date: 2015-07-30 07:30:11
---
I recently decided to reload my computer, moving from Windows 8.1 Enterprise Edition to Windows 10 Enterprise Edition. I had previously enabled the <a href="https://en.wikipedia.org/wiki/Data_deduplication" target="_blank">data deduplication</a> feature on my Windows 8.1 installation with an unsupported hack by using the source files from Server 2012 R2. Deduplication was enabled on my SSD drive for the VHDX files that I use for my test and demonstration environment that runs via Hyper-V VM's.

In my opinion, Microsoft should support data deduplication on enterprise edition desktop operating systems since it can save an enormous amount of space. Notice the size versus the size on disk in the following example:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/07/dedup-prob1a.png"><img class="alignnone size-full wp-image-12107" src="http://mikefrobbins.com/wp-content/uploads/2015/07/dedup-prob1a.png" alt="dedup-prob1a" width="364" height="456" /></a>

Deduplication has worked flawlessly on my computer while running Windows 8.1, but this is a perfect example of why you shouldn't do things that are unsupported even if they seem to work great in the short term. You're ultimately painting yourself into a corner and digging yourself a deeper hole because you'll experience a problem sooner or later.

There were some hacks to use dedup with some of the preview versions of Windows 10 because the necessary files were able to be used from the Windows Server Technical Preview, but those hacks don't seem to work with Windows 10 RTM because the Server files aren't the correct build version and the right ones don't seem to be available yet:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/07/dedup-prob4a.png"><img class="alignnone size-full wp-image-12118" src="http://mikefrobbins.com/wp-content/uploads/2015/07/dedup-prob4a.png" alt="dedup-prob4a" width="979" height="514" /></a>

The problem is the data can be seen on the drive, but it's inaccessible since Windows 10 doesn't have the necessary duplication feature installed. Here's the error when trying to connect a VHDX file in Windows 10 Hyper-V that exists on a dedup enabled volume:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/07/dedup-prob2a.png"><img class="alignnone size-full wp-image-12108" src="http://mikefrobbins.com/wp-content/uploads/2015/07/dedup-prob2a.png" alt="dedup-prob2a" width="353" height="267" /></a>
<h6><span style="color: #ff0000;">"The Virtual Machine Management service encounter an error while configuring the hard disk on virtual machine PC08.</span>
<span style="color: #ff0000;">'PC08' failed to add device 'Virtual Hard Disk'. (Virtual machine ID 421205ED-E92F-446-984B-12EBC9819296)</span>
<span style="color: #ff0000;">Failed to open attachment 'D:\Hyper-V\Virtual Hard Disks\pc08.vhdx'. Error: 'The file connect be accessed by the system.'.</span></h6>
Luckily, I had a Windows Server 2012 R2 machine that I was able to enable the deduplication feature on, move my SSD to, and copy the data off of it, then delete the partition and recreate it. This is the only way I've found to completely remove deduplication from a volume it has been enabled on since the <em>Disable-DedupVolume</em> cmdlet only disables deduplication from that point forward but doesn't undo data deduplication from existing data.

I did however run into one issue where one of my parent VHDX files was corrupted during the copy process. Notice the 1980 date on the file:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/07/dedup-prob3a.png"><img class="alignnone size-full wp-image-12109" src="http://mikefrobbins.com/wp-content/uploads/2015/07/dedup-prob3a.png" alt="dedup-prob3a" width="590" height="59" /></a>

This affected multiple VM's since I use differencing disks. Luckily I had a backup so I was able to restore that file and all seems to be well at this point except without deduplication.

The moral of the story is don't do things that aren't supported even if you can and they work great in the short term since that's not a good long term plan and you'll only be creating major headaches for yourself in the long term if you do so.

<span style="text-decoration: underline;">Update 7/31/15</span>
Fellow PowerShell MVP Stephen Owen posted a <a href="http://foxdeploy.com/2015/07/31/recovering-your-dedeuped-files-on-windows-10/" target="_blank">blog article</a> about this same issue along with a couple of possible solutions without having to physically move the drive to another machine. I would be curious to know what would happen if the unoptimization command was run and there was more data than would physically fit on the drive as was the case in my scenario.

µ