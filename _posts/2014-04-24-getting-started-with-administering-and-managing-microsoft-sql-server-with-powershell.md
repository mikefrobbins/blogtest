---
ID: 9722
post_title: >
  Getting Started with Administering and
  Managing Microsoft SQL Server with
  PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/04/24/getting-started-with-administering-and-managing-microsoft-sql-server-with-powershell/
published: true
post_date: 2014-04-24 07:30:52
---
I've used PowerShell to administer and manage Microsoft SQL Server for quite some time although I haven't blogged much about it. I thought I would take the time to write a series of blog articles about using PowerShell to administer and manage Microsoft SQL Server to help others who are just getting started and to clear up some common misconceptions.

Just to give you a little background information about myself, I've worked (professionally) in a data-center environment administering and managing every version of SQL Server since 6.5 and I hold certifications on each version beginning with 6.5 through 2008 to include MCDBA on SQL Server 2000, and MCITP on SQL Server 2005 and 2008.

The focus in this blog article series will be more on PowerShell than SQL Server so if you don't know anything about SQL Server, my recommendation is to learn more about the underlying technology (SQL Server) before attempting to learn how to administer and manage it with PowerShell.

The test environment for this scenario is a workstation named PC01 running a 64 bit version of Windows 8.1 (PowerShell version 4.0) with SQL Server 2014 Management Studio installed. I have two SQL Servers both running Windows Server 2012 R2 with the core installation (no GUI). The server named SQL01 has SQL Server 2014 installed. The other server is named SQL02 and has SQL Server 2008 R2 installed. Both have a default instance and at least one additional named instance of SQL Server installed.

I'll first start out by determining what PowerShell modules were added to PC01 when SQL Server Management Studio was installed on it. The SQL PowerShell Modules (or snap-ins depending on what version of SQL you're using) are actually installed as part of the SQL Client Connectivity SDK which was also installed (by default) when SQL Server Management Studio was installed on PC01.
<pre class="lang:ps decode:true">Get-Module -Name *SQL*</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_15-06-52.png"><img class="alignnone size-full wp-image-9745" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_15-06-52.png" alt="2014-04-23_15-06-52" width="877" height="155" /></a>

Now to see what commands (cmdlets, functions, etc) exist in those modules:
<pre class="lang:ps decode:true">Get-Command -Module (Get-Module -Name *SQL*) | Sort-Object -Property ModuleName, Name</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_15-12-04.png"><img class="alignnone size-full wp-image-9746" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_15-12-04.png" alt="2014-04-23_15-12-04" width="877" height="803" /></a>

The PowerShell modules for SQL Server 2014 contain a total of 57 SQL related commands:
<pre class="lang:ps decode:true">(Get-Command -Module (Get-Module -Name *SQL*)).count</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_15-14-46.png"><img class="alignnone size-full wp-image-9747" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_15-14-46.png" alt="2014-04-23_15-14-46" width="877" height="72" /></a>

If you happen to be using the PowerShell modules from SQL Server 2012, you'll only have a total of 41 commands:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_15-16-39.png"><img class="alignnone size-full wp-image-9748" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_15-16-39.png" alt="2014-04-23_15-16-39" width="877" height="71" /></a>

And if you happen to be using a version of SQL Server prior to 2012 that has PowerShell support, the PowerShell commands will be part of a Snap-in instead of a module. This example is from SQL Server 2008 R2 and you'll have very few commands to work with:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_15-03-17.png"><img class="alignnone size-full wp-image-9749" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_15-03-17.png" alt="2014-04-23_15-03-17" width="877" height="180" /></a>

Note: You can use the SQL Server 2012 or 2014 SQLPS module on your management workstation to access down level servers such as SQL Server 2008 R2. I haven't tested this on any servers at a lower level than that so your mileage may vary.

I've noticed some sort of weird auto-loading issue with the SQLPS module and my recommendation is to manually import the module before attempting to use any of the cmdlets in it and not to rely on the module auto-loading functionality which was first introduced in PowerShell version 3. I also had a chance to ask <a href="http://twitter.com/ScriptingGuys" target="_blank">Ed Wilson, The Scripting Guy</a> about this yesterday during his presentation for the <a href="http://powershell.sqlpass.org/" target="_blank">PowerShell Virtual Chapter of SQL PASS</a> and he made the same recommendation.

Explicitly import the SQLPS module:
<pre class="lang:ps decode:true">Import-Module -Name SQLPS</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_14-43-18.png"><img class="alignnone size-full wp-image-9740" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_14-43-18.png" alt="2014-04-23_14-43-18" width="877" height="98" /></a>

Notice a warning was returned in the previous example when the SQLPS module was imported. This is because there are a couple of cmdlets in that module that don't use approved verbs. Approved verbs is a subject for another blog article, but you can use the <em>-DisableNameChecking</em> switch parameter to prevent the warning from being displayed:
<pre class="lang:ps decode:true">Import-Module -Name SQLPS -DisableNameChecking</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_14-47-43.png"><img class="alignnone size-full wp-image-9741" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_14-47-43.png" alt="2014-04-23_14-47-43" width="877" height="63" /></a>

Note: The script execution policy on your management workstation (PC01 in this scenario) must be set to RemoteSigned (or lower) in order for the SQLPS module to load. Take a look at the <a href="http://technet.microsoft.com/en-us/library/hh847748.aspx" target="_blank">about_Execution_Policies</a> help topic in PowerShell before making this change.

One thing I did notice is the SQLPS module appears to be a 32 bit module. I haven't experienced any issues using it with a 64 bit version of PowerShell, but I'm curious if that may explain the intermittent issues with module auto-loading that I've experienced.
<pre class="lang:ps decode:true">Get-ChildItem -Path ($env:PSModulePath -split ';') -Filter *sql*</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_11-07-18.png"><img class="alignnone size-full wp-image-9727" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_11-07-18.png" alt="2014-04-23_11-07-18" width="877" height="192" /></a>

One of the ways that I've used PowerShell to administer and manage SQL Server is by simply using some of my existing T-SQL (Transact-SQL) with the <em>Invoke-Sqlcmd</em> cmdlet:
<pre class="lang:ps decode:true">Invoke-Sqlcmd -ServerInstance sql01 -Database master –Query 'select name, database_id, recovery_model_desc from sys.databases'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_20-52-53.png"><img class="alignnone size-full wp-image-9802" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_20-52-53.png" alt="2014-04-23_20-52-53" width="877" height="215" /></a>

An even better example of using the <em>Invoke-Sqlcmd</em> cmdlet can be found in a <a href="http://mikefrobbins.com/2012/10/30/use-powershell-to-create-active-directory-user-accounts-from-data-contained-in-the-adventure-works-2012-database/" target="_blank">previous blog article of mine</a> where I queried the AdventureWorks database for information to create 290 Active Directory user accounts which took a total of 23 seconds to complete.

Importing the SQLPS module also added a "SQLSERVER" PSDrive that can be used to administer and manage SQL Server as if it were a file system:
<pre class="lang:ps decode:true">Get-PSDrive</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_14-49-37.png"><img class="alignnone size-full wp-image-9742" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_14-49-37.png" alt="2014-04-23_14-49-37" width="877" height="275" /></a>

Now to return some useful information about the user databases on SQL01:
<pre class="lang:ps decode:true">Get-ChildItem -Path SQLSERVER:\SQL\SQL01\Default\DATABASES\ |
Select-Object -Property Name, Size, SpaceAvailable, DataSpaceUsage, IndexSpaceUsage |
Format-Table -AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_15-54-19.png"><img class="alignnone size-full wp-image-9751" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_15-54-19.png" alt="2014-04-23_15-54-19" width="877" height="194" /></a>

Then there are SQL Server Management Objects (SMO). One of the nice things about the SQLPS module in SQL Server 2012 and 2014 is there's no longer a need to manually reference the SQL Server assembly's to use SMO because they're automatically loaded when the SQLPS module is imported as you can see in the following example:
<pre class="lang:ps decode:true">[System.AppDomain]::CurrentDomain.GetAssemblies() | Where-Object FullName -like '*sqlserver*'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_16-06-27.png"><img class="alignnone size-full wp-image-9752" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_16-06-27.png" alt="2014-04-23_16-06-27" width="877" height="360" /></a>

That means once the SQLPS module is imported, you can work with SMO without having to use the <em>Add-Type</em> cmdlet or <em>[Reflection.Assembly]::LoadWithPartialName()</em> that most other blogs reference (although if you want to make your scripts backwards compatible, you'll still need to do it that way).
<pre class="lang:ps decode:true">$SQL = New-Object Microsoft.SqlServer.Management.Smo.Server -ArgumentList 'SQL01'
$SQL.Databases | Select-Object -Property Parent, Name, RecoveryModel</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_16-12-21.png"><img class="alignnone size-full wp-image-9753" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-23_16-12-21.png" alt="2014-04-23_16-12-21" width="877" height="216" /></a>

If you wanted to query the information for a named instance in the previous example, the value for <em>-ArgumentList</em> would have been 'ServerName\InstanceName'.

That's enough information for this blog article as I don't want to overwhelm you. I have some really neat stuff that I can't wait to share with you in future blog articles.

µ