---
ID: 10864
post_title: 'PowerShell One-Liner: Create a new Hyper-V VM'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/12/18/powershell-one-liner-create-a-new-hyper-v-vm/
published: true
post_date: 2014-12-18 07:30:46
---
My test environment resides on a workstation that runs Windows 8.1 Enterprise Edition with the Hyper-V role enabled. I need to create a new VM that will be used as a second domain controller in my test environment.

I'll use a PowerShell one-liner to create this new VM which will use a differencing disk based on a Windows Server 2012 R2 vhdx that has been fully patched and syspreped:
<pre class="lang:ps decode:true">New-VM -Name 'DC02' -VHDPath (
New-VHD -Differencing -ParentPath 'D:\Hyper-V\Virtual Hard Disks\template\Server2012R2Core-Template.vhdx' -Path 'D:\Hyper-V\Virtual Hard Disks\dc02.vhdx' -SizeBytes 127GB
).Path -MemoryStartupBytes 512MB -SwitchName 'Internal Virtual Switch' |
Set-VM -ProcessorCount 2 -DynamicMemory -MemoryMinimumBytes 512MB -MemoryMaximumBytes 2GB -Passthru |
Start-VM -Passthru</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/11/create-dc02-vm1.jpg"><img class="alignnone size-full wp-image-10869" src="http://mikefrobbins.com/wp-content/uploads/2014/11/create-dc02-vm1.jpg" alt="create-dc02-vm1" width="883" height="210" /></a>

µ