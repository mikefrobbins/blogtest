---
ID: 16123
post_title: >
  Use PowerShell to create a bootable USB
  drive from a Windows 10 or Windows
  Server 2016 ISO
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/01/18/use-powershell-to-create-a-bootable-usb-drive-from-a-windows-10-or-windows-server-2016-iso/
published: true
post_date: 2018-01-18 07:30:27
---
It seems as if every time I need to reload a physical system, I'm searching the Internet to find a way to create a bootable USB drive from a Windows 10 or Windows Server 2016 ISO. I always seem to find tutorials that are using a process that's almost 20 years old. They have me using the diskpart command line utility. Diskpart which initially shipped with Windows 2000, reminds me way too much of its predecessor, the fdisk command line utility.

The PowerShell commands in this blog article are written to be compatible with PowerShell version 4.0 and higher. Windows 8 or Windows Server 2012 with a GUI or higher is also required because although you can install PowerShell version 4.0+ on older operating systems, many of the commands used in this blog article won't exist on them. This is due to the newer WMI namespaces which the cmdlets rely on not existing on older operating systems. <em>Out-GridView</em> which is used in this blog article isn't supported on Server Core (Windows Server without a graphical user interface).

To create a bootable USB drive from an ISO, insert the USB drive into your computer and launch PowerShell as an administrator. Run the following PowerShell one-liner. <strong>Warning: This command will permanently delete all of the data on the selected USB drive. Proceed at your own risk. You've been warned.</strong>
<pre class="lang:ps decode:true">$Results = Get-Disk |
Where-Object BusType -eq USB |
Out-GridView -Title 'Select USB Drive to Format' -OutputMode Single |
Clear-Disk -RemoveData -RemoveOEM -Confirm:$false -PassThru |
New-Partition -UseMaximumSize -IsActive -AssignDriveLetter |
Format-Volume -FileSystem FAT32</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/boot-usb1b.jpg"><img class="alignnone size-full wp-image-16128" src="http://mikefrobbins.com/wp-content/uploads/2018/01/boot-usb1b.jpg" alt="" width="859" height="163" /></a>

Walking through the previous command, the first line gets a list of all the disks attached to the system. The second filters them to only ones that are USB drives. Those results are sent to <em>Out-GridView</em> for the user to select the USB drive to format in case more than one USB drive is attached to the system (<strong>you can only blame yourself if you select the wrong one</strong>).

<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/boot-usb2a.jpg"><img class="alignnone size-full wp-image-16129" src="http://mikefrobbins.com/wp-content/uploads/2018/01/boot-usb2a.jpg" alt="" width="859" height="183" /></a>

The fourth clears all data and partitions off of the disk. The fifth creates a new partition using all of the available space on the USB drive and assigns a drive letter to it. The last line formats the USB drive. While many tutorials use NTFS as the type of file system, I found that my Lenovo ThinkPad P50 will not boot from a Windows 10 USB drive formatted with NTFS. It boots fine from one formatted with FAT32. Trying to use FAT32 for Windows Server 2016 generates an error though, so you'll need to use NTFS for it.

<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/boot-usb5a.jpg"><img class="alignnone size-full wp-image-16184" src="http://mikefrobbins.com/wp-content/uploads/2018/01/boot-usb5a.jpg" alt="" width="859" height="94" /></a>

Mount the ISO. The problem I ran into is there's no easy way to determine what drive letter is assigned to an ISO once it's mounted. The simplest way I found is to compare the drive letters before and after mounting it. While this sounds simple, another problem I ran into was that <a href="http://mikefrobbins.com/2018/01/08/powershell-compare-object-doesnt-handle-null-values/" target="_blank" rel="noopener">Compare-Object doesn’t handle null values</a>.
<pre class="lang:ps decode:true">$Volumes = (Get-Volume).Where({$_.DriveLetter}).DriveLetter
Mount-DiskImage -ImagePath C:\ISO\SW_DVD5_Win_Pro_Ent_Edu_N_10_1709_64BIT_English_MLF_X21-50143.ISO
$ISO = (Compare-Object -ReferenceObject $Volumes -DifferenceObject (Get-Volume).Where({$_.DriveLetter}).DriveLetter).InputObject</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/boot-usb3a.jpg"><img class="alignnone size-full wp-image-16130" src="http://mikefrobbins.com/wp-content/uploads/2018/01/boot-usb3a.jpg" alt="" width="859" height="91" /></a>

Change directory into the boot folder on the drive of the mounted ISO. Make the drive bootable and copy the contents of the ISO to the USB drive.
<pre class="lang:ps decode:true ">Set-Location -Path "$($ISO):\boot"
bootsect.exe /nt60 "$($Results.DriveLetter):"
Copy-Item -Path "$($ISO):\*" -Destination "$($Results.DriveLetter):" -Recurse -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/boot-usb4b.jpg"><img class="alignnone size-full wp-image-16190" src="http://mikefrobbins.com/wp-content/uploads/2018/01/boot-usb4b.jpg" alt="" width="859" height="247" /></a>

While I've tried to do as much of this process as possible in PowerShell, there's still a need to use a few command line utilities to accomplish this task. Thankfully, it's something that you shouldn't have to do very often. For ease of reuse, label your USB drive with a label maker, not a permanent marker.

The original version of this blog article used xcopy.exe to copy the files. That portion of this blog article has been updated to use the PowerShell <em>Copy-Item</em> cmdlet.

µ