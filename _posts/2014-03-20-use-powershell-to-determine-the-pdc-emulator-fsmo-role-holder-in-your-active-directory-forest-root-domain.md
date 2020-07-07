---
ID: 9310
post_title: >
  Use PowerShell to Determine the PDC
  Emulator FSMO Role Holder in your Active
  Directory Forest Root Domain
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/03/20/use-powershell-to-determine-the-pdc-emulator-fsmo-role-holder-in-your-active-directory-forest-root-domain/
published: true
post_date: 2014-03-20 07:30:08
---
Each domain has a PDC emulator FSMO role so how do I determine which domain controller holds the PDC emulator FSMO role in the forest root domain if I have multiple domains in my forest? Sounds like you can't see the forest root for the trees :-).

The answer of course is with PowerShell:
<pre class="lang:ps decode:true" title="PDC Emulator in Forest Root Domain">Get-ADForest |
Select-Object -ExpandProperty RootDomain |
Get-ADDomain |
Select-Object -Property PDCEmulator</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-19_20-29-19.png"><img class="alignnone size-full wp-image-9313" alt="2014-03-19_20-29-19" src="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-19_20-29-19.png" width="877" height="179" /></a>

The Active Directory PowerShell module which is part of the Remote Server Administration Tools (RSAT) is installed on the workstation these commands are being run from. The module is automatically imported since the workstation is running a new enough version of PowerShell to take advantage of the module auto-loading feature that was first introduced in PowerShell version 3.

µ