---
ID: 15123
post_title: >
  Convert, Resize, and Optimize VHD and
  VHDX files with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/03/23/convert-resize-and-optimize-vhd-and-vhdx-files-with-powershell/
published: true
post_date: 2017-03-23 07:30:31
---
I recently received an email from someone who attended one of my presentations asking if I had a blog article on using PowerShell to compact and optimize VHD files. Since I didn't have a blog article on that subject, I decided to create one.

The process itself is fairly simple. The examples shown in this blog article are being run on a Windows 10 computer which has Hyper-V enabled on it. Only specific SKU's of Windows 10 are capable of running Hyper-V. The same process can be used on servers running Windows Server 2012 R2 or Windows Server 2016 (and possibly on other versions, but I can't confirm that).

There are several PowerShell cmdlets for working with VHD files as shown in the following example.
<pre class="lang:ps decode:true">Get-Command -Noun VHD* -Module Hyper-V</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/03/optimize-vhd1a.png"><img class="alignnone size-full wp-image-15124" src="http://mikefrobbins.com/wp-content/uploads/2017/03/optimize-vhd1a.png" alt="" width="859" height="284" /></a>

The <em>Convert-VHD</em> cmdlet can be used to change the format (VHD to VHDX or vise-versa), Type (fixed, dynamic, and differencing), and block size of the file.

The following example converts a VHD file to a VHDX file.
<pre class="lang:ps decode:true ">Convert-VHD -Path 'D:\Hyper-V\Virtual Hard Disks\nano2.vhd' -DestinationPath 'D:\Hyper-V\Virtual Hard Disks\nano2.vhdx'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/03/optimize-vhd2a.png"><img class="alignnone size-full wp-image-15126" src="http://mikefrobbins.com/wp-content/uploads/2017/03/optimize-vhd2a.png" alt="" width="859" height="132" /></a>

There are a number of parameters for the <em>Convert-VHD</em> cmdlet so be sure to take a look at the <a href="https://technet.microsoft.com/en-us/itpro/powershell/windows/hyper-v/convert-vhd" target="_blank">help</a> for it. The <em>BlockSizeBytes</em> parameter is used to change the block size, the <em>DeleteSource</em> parameter is used to delete the source file once the destination file is created, and the VHDType parameter is used to change the type of VHD (fixed, dynamic, or differencing).

One thing to keep in mind is the conversion process is an offline operation. The help states that the file being converted can't be attached, but what I've found is that it can be attached as long as the VM (virtual machine) isn't running.

The <em>Optimize-VHD</em> cmdlet is used to optimize the amount of space used by dynamic virtual hard drive files. When this cmdlet is run, it compacts the virtual file as shown in the following example.
<pre class="lang:ps decode:true "> Optimize-VHD -Path 'D:\Hyper-V\Virtual Hard Disks\srv1e.vhd'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/03/optimize-vhd3a.png"><img class="alignnone size-full wp-image-15129" src="http://mikefrobbins.com/wp-content/uploads/2017/03/optimize-vhd3a.png" alt="" width="859" height="131" /></a>

Based on the information found in the <a href="https://technet.microsoft.com/en-us/itpro/powershell/windows/hyper-v/optimize-vhd" target="_blank">help</a>, this cmdlet not only reclaims unused blocks, but it also rearranges the blocks to be more efficiently packed, which also reduces the size of the VHD or VHDX. It's possible for this command to complete without shrinking the virtual file if no optimization is necessary. The Mode parameter can be used to change the default mode which is Full for VHD files and Quick for VHDX files.

The <em>Resize-VHD</em> cmdlet is used to shrink VHDX files or expand both VHD and VHDX files. The shrink operation will fail if a size smaller than the minimum size is specified as shown in the following example.
<pre class="lang:ps decode:true">Get-VHD -Path 'D:\Hyper-V\Virtual Hard Disks\srv1e.vhd'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/03/optimize-vhd4a.png"><img class="alignnone size-full wp-image-15131" src="http://mikefrobbins.com/wp-content/uploads/2017/03/optimize-vhd4a.png" alt="" width="859" height="321" /></a>

The <em>MinimumSize</em> parameter can be used to shrink a VHDX to it's minimum size without having to worry about the possibility of specifying a file size that's too small.
<pre class="lang:ps decode:true">Resize-VHD -Path 'D:\Hyper-V\Virtual Hard Disks\srv1.vhdx' -ToMinimumSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/03/optimize-vhd5a.png"><img class="alignnone size-full wp-image-15136" src="http://mikefrobbins.com/wp-content/uploads/2017/03/optimize-vhd5a.png" alt="" width="859" height="58" /></a>

Be sure to take a look at the <a href="https://technet.microsoft.com/en-us/itpro/powershell/windows/hyper-v/resize-vhd" target="_blank">help</a> for the <em>Resize-VHD</em> cmdlet to determine all of the available options.

µ