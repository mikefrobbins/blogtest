---
ID: 4174
post_title: >
  Symantec Backup Exec 2012 Adds
  PowerShell Support!
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/05/08/symantec-backup-exec-2012-adds-powershell-support/
published: true
post_date: 2012-05-08 07:30:18
---
The latest version of Backup Exec, version 2012 adds support for PowerShell. When Backup Exec 2012 is installed, it adds a PowerShell module named "BEMCLI":

<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/bemcli-1.png"><img class="alignnone size-full wp-image-4175" title="bemcli-1" src="http://mikefrobbins.com/wp-content/uploads/2012/05/bemcli-1.png" alt="" width="436" height="218" /></a>

You'll need the .NET Framework 3.5.1 installed to be able to import this module:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/bemcli-2.png"><img class="alignnone size-full wp-image-4176" title="bemcli-2" src="http://mikefrobbins.com/wp-content/uploads/2012/05/bemcli-2.png" alt="" width="640" height="135" /></a>

I'm guessing this .NET Framework 3.5.1 issue is an oversight since the typical installation installs the .NET Framework 4.0, but doesn't enable 3.5.1:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/bemcli21.png"><img class="alignnone size-full wp-image-4197" title="bemcli21" src="http://mikefrobbins.com/wp-content/uploads/2012/05/bemcli21.png" alt="" width="640" height="291" /></a>

Enable the NET-Framework-Core Windows Feature. You'll have to import the server manager module (<em>Import-Module ServerManager</em>) first if adding it through PowerShell:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/bemcli-32.png"><img class="alignnone size-full wp-image-4187" title="bemcli-32" src="http://mikefrobbins.com/wp-content/uploads/2012/05/bemcli-32.png" alt="" width="640" height="187" /></a>

Close out of PowerShell and reopen it after adding the .NET Framework to prevent the errors shown above. The BEMCLI module adds 203 PowerShell cmdlets:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/bemcli-41.png"><img class="alignnone size-full wp-image-4180" title="bemcli-41" src="http://mikefrobbins.com/wp-content/uploads/2012/05/bemcli-41.png" alt="" width="564" height="102" /></a>

Looks like a lot of fun for us PowerShell Enthusiasts. Here's a list of the "Get" cmdlets:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/bemcli-52.png"><img class="alignnone size-full wp-image-4182" title="bemcli-52" src="http://mikefrobbins.com/wp-content/uploads/2012/05/bemcli-52.png" alt="" width="640" height="571" /></a>

There will definitely be more blog articles about this PowerShell Module. I'm happy to see that more vendors are seeing the benefits of adding PowerShell support to their products.

Update 5/19/2012

You'll also need to set the script execution policy on the system you're importing the BEMCLI PowerShell module on otherwise you'll receive an error when trying to import it:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/import-bemcli.png"><img class="alignnone size-full wp-image-4240" title="import-bemcli" src="http://mikefrobbins.com/wp-content/uploads/2012/05/import-bemcli.png" alt="" width="640" height="150" /></a>

µ