---
ID: 3394
post_title: 'Get-WmiObject : The RPC Server is Unavailable. (Exception from HRESULT: 0x800706BA)'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/03/08/get-wmiobject-the-rpc-server-is-unavailable-exception-from-hresult-0x800706ba/
published: true
post_date: 2012-03-08 07:30:09
---
This week while working with some PowerShell scripts that retrieve <a href="http://en.wikipedia.org/wiki/Windows_Management_Instrumentation" target="_blank">WMI</a> information from remote servers, I ran into an issue where I was able to return results from Windows 2008 R2 servers without issue but was unable to return results from Windows 2008 (non-R2) servers even though they had PowerShell 2.0 installed and as far as I could tell they were configured exactly the same as the Windows 2008 R2 servers.

Here's the error I received:

<em><span style="color:#ff0000;">Get-WmiObject : The RPC server is unavailable. (Exception from HRESULT: 0x800706BA)</span></em>
<em><span style="color:#ff0000;">At D:Get-MachineInfo.ps1:18 char:14</span></em>
<em><span style="color:#ff0000;">+ Get-WmiObject &lt;&lt;&lt;&lt; -class Win32_OperatingSystem `</span></em>
<em><span style="color:#ff0000;"> + CategoryInfo : InvalidOperation: (:) [Get-WmiObject], COMException</span></em>
<em><span style="color:#ff0000;"> + FullyQualifiedErrorId : GetWMICOMException,Microsoft.PowerShell.Commands.GetWmiObjectCommand
</span></em><a href="http://mikefrobbins.com/wp-content/uploads/2012/03/gwmi-error1.png"><img class="alignnone size-full wp-image-3395" title="gwmi-error1" src="http://mikefrobbins.com/wp-content/uploads/2012/03/gwmi-error1.png" alt="" width="640" height="157" /></a>

Enabling "Remote Administration" in the firewall on the Windows Server 2008 (non-R2) servers resolved this issue.
<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/gwmi-error2.png"><img class="alignnone size-full wp-image-3396" title="gwmi-error2" src="http://mikefrobbins.com/wp-content/uploads/2012/03/gwmi-error2.png" alt="" width="432" height="507" /></a>

I verified this firewall exception was not allowed on the Windows 2008 R2 servers since those worked without issue, but it does appear that it's required on the non-R2 servers.

µ