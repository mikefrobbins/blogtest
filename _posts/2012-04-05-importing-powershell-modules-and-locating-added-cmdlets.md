---
ID: 3479
post_title: >
  Importing PowerShell Modules and
  Locating Added Cmdlets
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/04/05/importing-powershell-modules-and-locating-added-cmdlets/
published: true
post_date: 2012-04-05 07:30:55
---
Want to add a feature to a Windows Server 2008 R2 machine using PowerShell? That functionality is part of the ServerManager PowerShell Module that's install by default on 2008 R2. The module has to be imported for it's cmdlets to be made available since it's not loaded by default when you launch PowerShell.

To view the Modules that are available to be imported, run <em>Get-Module -ListAvailable</em>

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-1.png"><img class="alignnone size-full wp-image-3482" title="ps103-1" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-1.png" alt="" width="392" height="172" /></a>

The ServerManager module has to be imported so that it's commands are made available to PowerShell. This is done by running <em>Import-Module ServerManager</em> as shown below.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-21.png"><img class="alignnone size-full wp-image-3555" title="ps103-21" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-21.png" alt="" width="255" height="13" /></a>

Use Get-Module with no parameters to display all of the modules that have been imported into PowerShell. If more than the ServerManager module were imported, they would be listed in the image below. Modules you import are only imported for the duration that the shell is open. The next time you open PowerShell, you'll have to re-import the ServerManager module if you want to use it's cmdlets again. Creating a PowerShell Profile can allow you to automatically import commonly used modules when you launch PowerShell but that's a topic for another blog article.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-23.png"><img class="alignnone size-full wp-image-3628" title="ps103-23" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-23.png" alt="" width="583" height="73" /></a>

To find out what commands the ServerManager Module added, run <em>Get-Command -Module ServerManager</em> as shown in the screenshot below:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-3.png"><img class="alignnone size-full wp-image-3484" title="ps103-3" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-3.png" alt="" width="300" height="97" /></a>

Want to remotely administer Active Directory from a Windows 7 machine using PowerShell? You'll need to download the <a href="http://www.microsoft.com/download/en/details.aspx?id=7887" target="_blank">Remote Server Administration Tools</a> from the Microsoft Download Center, install it, and then enable the "Active Directory Module for Windows PowerShell" feature on the Windows 7 computer.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-4.png"><img class="alignnone size-full wp-image-3485" title="ps103-4" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-4.png" alt="" width="429" height="375" /></a>

Then you'll be able to import the ActiveDirectory module:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-8.png"><img class="alignnone size-full wp-image-3493" title="ps103-8" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-8.png" alt="" width="269" height="114" /></a>

Determine what cmdlets were added by the ActiveDirectory module:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-9.png"><img class="alignnone size-full wp-image-3494" title="ps103-9" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-9.png" alt="" width="458" height="236" /></a>

Some vendors such as EqualLogic provide PowerShell modules to manage their products with. Install the PowerShell modules provided by the vendor:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-5.png"><img class="alignnone size-full wp-image-3486" title="ps103-5" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-5.png" alt="" width="514" height="392" /></a>

Sometimes these vendor modules aren't installed in the locations that PowerShell looks for Modules in so it's unaware that they're available for import and they won't show up in the results of <em>Get-Module -ListAvailable</em>. Look at the properties of the shortcut that the product created to determine what needs to be imported.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-6.png"><img class="alignnone size-full wp-image-3487" title="ps103-6" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-6.png" alt="" width="386" height="533" /></a>

Use the path and filename to the EqlPsTools.dll to load this module:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-7.png"><img class="alignnone size-full wp-image-3488" title="ps103-7" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-7.png" alt="" width="507" height="113" /></a>

Use the same process to determining what new cmdlets were added by this module:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-10.png"><img class="alignnone size-full wp-image-3495" title="ps103-10" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps103-10.png" alt="" width="344" height="365" /></a>

I'll cover PowerShell Snap-ins in my next blog article.

µ