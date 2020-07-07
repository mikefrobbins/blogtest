---
ID: 17476
post_title: >
  Resolving Microsoft SQL Server Error
  4064 with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/01/11/resolving-microsoft-sql-server-error-4064-with-powershell/
published: true
post_date: 2019-01-11 07:30:54
---
Recently, a fellow IT Pro contacted me and stated they were unable to login to one of their SQL Servers using Windows Authentication. The following error was generated when attempting to login to SQL Server Management Studio (SSMS).

<a href="https://mikefrobbins.com/wp-content/uploads/2019/01/sql-error-4064-1a.jpg"><img class="alignnone size-full wp-image-17477" src="https://mikefrobbins.com/wp-content/uploads/2019/01/sql-error-4064-1a.jpg" alt="" width="607" height="169" /></a>

Their exact words were "I think we have a permissions problem". Clicking on the "Show technical details" icon at the bottom of that error message showed the following information.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/01/sql-error-4064-2a.jpg"><img class="alignnone size-full wp-image-17480" src="https://mikefrobbins.com/wp-content/uploads/2019/01/sql-error-4064-2a.jpg" alt="" width="612" height="194" /></a>

You can work around this problem by clicking on the "Options &gt;&gt;" button:

<a href="https://mikefrobbins.com/wp-content/uploads/2019/01/sql-error-4064-3a.jpg"><img class="alignnone size-full wp-image-17481" src="https://mikefrobbins.com/wp-content/uploads/2019/01/sql-error-4064-3a.jpg" alt="" width="476" height="314" /></a>

Then changing the "Connnect to database" to one that exists and that you have access to:

<a href="https://mikefrobbins.com/wp-content/uploads/2019/01/sql-error-4064-4a.jpg"><img class="alignnone size-full wp-image-17482" src="https://mikefrobbins.com/wp-content/uploads/2019/01/sql-error-4064-4a.jpg" alt="" width="478" height="520" /></a>

In this scenario, what had happened is the users' default database had been removed from this server and migrated to a new SQL Server. While it's easy enough to change a users default database in SSMS, what if you have numerous users to change it for?

I'll be using <a href="https://dbatools.io/" target="_blank" rel="noopener">the dbatools PowerShell module</a> which can be installed from <a href="https://www.powershellgallery.com/packages/dbatools/" target="_blank" rel="noopener">the PowerShell Gallery</a>. The <em>Install-Module</em> function used in the following example is part of the PowerShellGet module which exists by default on PowerShell version 5.0 and higher.
<pre class="lang:ps decode:true">Install-Module -Name dbatools -Force</pre>
<blockquote>To be clear, the dbatools PowerShell module is being installed on a Windows 10 workstation and its commands will be run remotely against the specified instance of SQL Server.</blockquote>
First, the following command gets a list of all the databases on the specified SQL instance and stores them in a variable. Then, it queries the instance for a list of SQL logins where the user is set to a database that doesn't exist on that instance.
<pre class="lang:ps decode:true ">$Databases = (Get-DbaDatabase -SqlInstance sql08).Name

Get-DbaLogin -SqlInstance sql08 -ExcludeSystemLogin |
Where-Object DefaultDatabase -notin $Databases |
Select-Object -Property Name, LoginType, DefaultDatabase</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/01/sql-error-4064-5a.jpg"><img class="alignnone size-full wp-image-17485" src="https://mikefrobbins.com/wp-content/uploads/2019/01/sql-error-4064-5a.jpg" alt="" width="859" height="522" /></a>

While this same command could be written as a one-liner as shown in the following example, the one-liner would be less efficient because it would query the SQL instance for a list of databases for every single user instead of querying them one time, storing them in a variable, and then using the contents of the variable for the comparison as shown in the previous example.
<pre class="lang:ps decode:true ">Get-DbaLogin -SqlInstance sql08 -ExcludeSystemLogin |
Where-Object DefaultDatabase -notin (Get-DbaDatabase -SqlInstance sql08).Name |
Select-Object -Property Name, LoginType, DefaultDatabase</pre>
To resolve the login problem for all of these SQL Server logins, their default database needs to be changed to one that exists. I searched through numerous commands from different PowerShell modules and didn't find one to accomplish the task of setting a users default database except upon initial creation.

Instead of resorting to leveraging SQL Server Management Objects (SMO) directly with PowerShell, I decided to <a href="https://github.com/sqlcollaborative/dbatools/pull/4930" target="_blank" rel="noopener">add this functionality to the Set-DbaLogin command</a> that's part of the dbatools module. If I had a need for it, I'm sure others do as well.
<pre class="lang:ps decode:true">Get-DbaLogin -SqlInstance sql08 -ExcludeSystemLogin |
Where-Object DefaultDatabase -notin $Databases |
Set-DbaLogin -DefaultDatabase master</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/01/sql-error-4064-6a.jpg"><img class="alignnone size-full wp-image-17489" src="https://mikefrobbins.com/wp-content/uploads/2019/01/sql-error-4064-6a.jpg" alt="" width="859" height="522" /></a>

One thing that I don't like about the <em>Set-DbaLogin</em> command is that it returns results by default. Most commands that I've used from other PowerShell modules including the default ones that set something usually don't return results unless a <em>PassThru</em> parameter is specified.

One workaround for this if you don't want results is to simply pipe the results to <em>Out-Null</em>. I know that using <em>[void]</em> or assigning it to the <em>$null</em> variable is faster, but <em>Out-Null</em> feels more like the PowerShell way to me.
<pre class="lang:ps decode:true">Get-DbaLogin -SqlInstance sql08 -ExcludeSystemLogin |
Where-Object DefaultDatabase -notin $Databases |
Set-DbaLogin -DefaultDatabase master |
Out-Null</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/01/sql-error-4064-7a.jpg"><img class="alignnone size-full wp-image-17490" src="https://mikefrobbins.com/wp-content/uploads/2019/01/sql-error-4064-7a.jpg" alt="" width="859" height="105" /></a>

Now there are no logins set to databases that don't exist on this instance of SQL Server.
<pre class="lang:ps decode:true ">Get-DbaLogin -SqlInstance sql08 -ExcludeSystemLogin |
Where-Object DefaultDatabase -notin $Databases</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/01/sql-error-4064-8a.jpg"><img class="alignnone size-full wp-image-17491" src="https://mikefrobbins.com/wp-content/uploads/2019/01/sql-error-4064-8a.jpg" alt="" width="859" height="77" /></a>

I also ended up finding <a href="https://github.com/sqlcollaborative/dbatools/pull/4934" target="_blank" rel="noopener">a minor bug (typo) in the <em>New-DbaLogin</em> command</a> and fixed it along the way.

µ