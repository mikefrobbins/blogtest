---
ID: 3511
post_title: 'Adding PowerShell Snap-in&#8217;s and Locating Added Cmdlets'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/05/29/adding-powershell-snap-ins-and-locating-added-cmdlets/
published: true
post_date: 2012-05-29 07:30:05
---
Want to make the SharePoint 2010 cmdlets available for use from the PowerShell ISE? Some products such as SharePoint 2010 provide their application specific PowerShell cmdlets via a PowerShell snap-in instead of a PowerShell module. The following commands are being run from the PowerShell ISE on a SharePoint 2010 web front-end server.

To view the snap-ins that are available to add, run the following:
<pre class="lang:ps decode:true">Get-PSSnapin -Registered</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps104-3.png"><img class="alignnone size-full wp-image-3515" title="ps104-3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps104-3.png" width="591" height="88" /></a>

The SharePoint 2010 snap-in is installed on the machine, but its cmdlets are not yet available for use. The snap-in has to be added so that its cmdlets are made available for use in this PowerShell session. Adding a snap-in is roughly the equivalent of importing a PowerShell module. Adding it is done by running <em>Add-PSSnapin "Snap-in Name"</em> as shown below. This only adds the snap-in for this session, if the PowerShell ISE is closed and re-opened the snap-in will have to be re-added. You can have snap-ins added automatically by adding this command to your profile, but that's a subject for another blog.
<pre class="lang:ps decode:true">Add-PSSnapin Microsoft.SharePoint.PowerShell</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps104-5.png"><img class="alignnone size-full wp-image-3517" title="ps104-5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps104-5.png" width="427" height="34" /></a>

Running <em>Get-PSSnapin</em> with no parameters shows the PowerShell snap-ins that have been added. All of the cmdlets included in each of these snap-ins are available for use.
<pre class="lang:ps decode:true">Get-PSSnapin</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps104-6.png"><img class="alignnone size-full wp-image-3518" title="ps104-6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps104-6.png" width="592" height="508" /></a>

Use the <em>Get-Command</em> cmdlet to determine what cmdlets were added by a particular snap-in. Even though this is a snap-in, the <em>-Module</em> parameter is used.
<pre class="lang:ps decode:true">help Get-Command -Full</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps104-7.png"><img class="alignnone size-full wp-image-3519" title="ps104-7" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps104-7.png" width="640" height="170" /></a>

Viewing the help for the <em>-Module</em> parameter of <em>Get-Command</em> confirms that it also works with snap-ins:
<pre class="lang:ps decode:true">help Get-Command -Parameter Module</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps104-8.png"><img class="alignnone size-full wp-image-3520" title="ps104-8" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps104-8.png" width="519" height="214" /></a>

Run <em>Get-Command -Module Microsoft.SharePoint.PowerShell</em> to view the cmdlets that were added by this snap-in (this will also show aliases, functions, etc that were added since I didn't specify the "<em>-CommandType Cmdlet</em>" parameter):
<pre class="lang:ps decode:true">Get-Command -Module Microsoft.SharePoint.PowerShell</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps104-9.png"><img class="alignnone size-full wp-image-3521" title="ps104-9" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps104-9.png" width="488" height="476" /></a>

If you know a cmdlet name and want to know what snap-in added it, you can do what I call a "Reverse Lookup" by using the following command:
<pre class="lang:ps decode:true">gcm Get-SPBackupHistory |
select pssnapin</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/ps104-91.png"><img class="alignnone size-full wp-image-4287" title="ps104-91" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/05/ps104-91.png" width="242" height="88" /></a>

The <em>-Name</em> parameter of the <em>Add-PSSnapin</em> cmdlet doesn't support wildcards, but they can be used with the <em>Get-PSSnapin</em> cmdlet and it can be piped to <em>Add-PSSnapin</em>. Use the <em>-PassThru</em> parameter to return the snap-ins that were added:
<pre class="lang:ps decode:true">Get-PSSnapin -Name "Sql*" -Registered |
Add-PSSnapin -PassThru</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/ps104-10.png"><img class="alignnone size-full wp-image-4283" title="ps104-10" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/05/ps104-10.png" width="613" height="158" /></a>

µ