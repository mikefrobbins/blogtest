---
ID: 15195
post_title: >
  PowerShell One-Liner to Query multiple
  WMI Classes with a CIM Session
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/04/27/powershell-one-liner-to-query-multiple-wmi-classes-with-a-cim-session/
published: true
post_date: 2017-04-27 07:30:58
---
Today I thought I would share a one-liner that I recently wrote to query the Manufacturer, Model, and Serial Number information from numerous remote servers. Sounds simple enough, right?

This one-liner starts out by using <a href="http://mikefrobbins.com/2014/08/28/powershell-function-to-create-cimsessions-to-remote-computers-with-fallback-to-dcom/" target="_blank" rel="noopener noreferrer">my <em>New-MrCimSession</em> function</a> to create a CIM session to each of the specified servers. This function automatically determines if the remote server supports the WSMan protocol and falls back to DCom if it doesn't:
<pre class="lang:ps decode:true">Get-CimSession | Select-Object -Property Name, ComputerName, Protocol</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/04/cim-oneliner2a.png"><img class="alignnone size-full wp-image-15198" src="http://mikefrobbins.com/wp-content/uploads/2017/04/cim-oneliner2a.png" alt="" width="859" height="163" /></a>

Two different WMI classes  are needed to retrieve the necessary information. The manufacturer and serial number can be retrieved from the Win32_BIOS class and the model can be retrieved from the Win32_ComputerSystem class. Retrieving this information with a script or function and combining the results from both classes into a custom object would be simple, but it's a little more challenging to retrieving this information with a one-liner.
<pre class="lang:ps decode:true">New-MrCimSession -ComputerName sql02, sql03, sql04, web01 -Credential (Get-Credential) |
Get-CimInstance -ClassName Win32_BIOS -Property Manufacturer, SerialNumber |
Select-Object -Property PSComputerName, Manufacturer,
                        @{label='Model';expression={(Get-CimInstance -CimSession (
                            Get-CimSession | Where-Object ComputerName -eq $_.PSComputerName
                            ) -ClassName Win32_ComputerSystem -Property Model).Model}},
                        SerialNumber</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/04/cim-oneliner1a.png"><img class="alignnone size-full wp-image-15197" src="http://mikefrobbins.com/wp-content/uploads/2017/04/cim-oneliner1a.png" alt="" width="859" height="285" /></a>

The challenging part is reusing the CIM session with <em>Select-Object</em> to query the second WMI class while creating a custom object with a one-liner.

Be sure to remove those CIM sessions when you're finished:
<pre class="lang:ps decode:true">Get-CimSession | Remove-CimSession</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/04/cim-oneliner3a.png"><img class="alignnone size-full wp-image-15200" src="http://mikefrobbins.com/wp-content/uploads/2017/04/cim-oneliner3a.png" alt="" width="859" height="58" /></a>

The CIM cmdlets were first introduced with PowerShell version 3. The computer you're performing the query from must have at least PowerShell version 3 installed, but the remote computer that the query is being run against doesn't need PowerShell installed at all since using <em>Get-CimInstance</em> with a CIM session allows it to fall back to using the DCom protocol.

The <em>New-MrCimSession</em> function shown in the blog article can be downloaded from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank" rel="noopener noreferrer">my PowerShell repository on GitHub</a> or <a href="https://www.powershellgallery.com/packages/MrToolkit/1.2" target="_blank" rel="noopener noreferrer">installed as part of my MrToolkit module from the PowerShell Gallery</a>.

µ