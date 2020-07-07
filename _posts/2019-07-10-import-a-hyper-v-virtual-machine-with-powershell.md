---
ID: 17929
post_title: >
  Import a Hyper-V Virtual Machine with
  PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/07/10/import-a-hyper-v-virtual-machine-with-powershell/
published: true
post_date: 2019-07-10 07:30:41
---
I recently ran into a problem where an exported VM from Windows Server 2016 running Hyper-V wasn't able to be imported on Windows Server 2019 running Hyper-V. When attempting to perform the import, the GUI stated "No virtual machines files found" as shown in the following image. This problem may have been due to using Hyper-V manager on a remote system running Windows Server 2012 R2, although the same system was used for the export.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/07/hyperv-import-vm1a.jpg"><img class="alignnone size-full wp-image-17931" src="https://mikefrobbins.com/wp-content/uploads/2019/07/hyperv-import-vm1a.jpg" alt="" width="718" height="540" /></a>

Since the Hyper-V servers themselves were installed with the Server Core installation option (no GUI), I decided to try importing the VM with PowerShell instead. I was able to successfully import it using the following command.
<pre class="lang:ps decode:true">Import-VM -Path '.\Virtual Machines\BD64EDC0-CD9B-4EFE-8F42-D3EBD9A6F522.vmcx' -Copy -GenerateNewId -VirtualMachinePath D:\ -VhdDestinationPath 'D:\Server01\Virtual Hard Disks\' -SnapshotFilePath D:\Server01\ -SmartPagingFilePath D:\Server01\</pre>
What I also learned is that if the Hyper-V configuration files exist on a volume on a storage device such as a SAN and that entire volume is connected to the new server, you can simply import the VM without needing to perform an export first. It appears that all an export does is copy the VHD/VHDX files, configuration files, snapshots, etc.
<pre class="lang:ps decode:true ">Import-VM -Path '.\Virtual Machines\BD64EDC0-CD9B-4EFE-8F42-D3EBD9A6F522.vmcx' -Register</pre>
The previous command performs a "Register in-place" of the VM instead of performing a copy of the files. It assumes that you already have the correct file structure in place as would be the case for performing a simple migration of a storage volume and VM to a new Hyper-V server.

More information can be found in this Microsoft "<a href="https://docs.microsoft.com/en-us/windows-server/virtualization/hyper-v/deploy/export-and-import-virtual-machines" target="_blank" rel="noopener noreferrer">Export and Import virtual machines</a>" article and the PowerShell help topic for <a href="https://docs.microsoft.com/en-us/powershell/module/hyper-v/import-vm?view=win10-ps" target="_blank" rel="noopener noreferrer"><em>Import-VM</em></a>.

Thoughts, questions, comments? Please post them as a comment to this blog article.

µ