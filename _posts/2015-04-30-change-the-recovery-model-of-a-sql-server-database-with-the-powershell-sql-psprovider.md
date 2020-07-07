---
ID: 11583
post_title: >
  Change the Recovery Model of a SQL
  Server database with the PowerShell SQL
  PSProvider
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/04/30/change-the-recovery-model-of-a-sql-server-database-with-the-powershell-sql-psprovider/
published: true
post_date: 2015-04-30 07:30:03
---
I recently set out to change the recovery model of a SQL Server database with PowerShell. There seems to be lots of information available on how to accomplish this task with PowerShell through SMO (SQL Server Management Objects) and using T-SQL wrapped inside the <em>Invoke-Sqlcmd</em> cmdlet. I even found lots of information about how to view the recovery model with the PowerShell SQL Server PSProvider, but when it came to actually changing the recovery model via the PSProvider, there was little if any information about how to accomplish that task.

The workstation used in this blog article is running Windows 8.1 with PowerShell version 4 which is the default version of PowerShell that ships with Windows 8.1. It has the SQL 2014 Management Tools installed. The server which is named SQL02 is running Windows Server 2012 R2, PowerShell version 4 is also the default version that ships with that operating system. The server is running SQL Server 2008 R2.

SQL Server 2012 and higher uses a PowerShell module and since I have the SQL 2014 management tools installed on the workstation, it has the SQLPS module installed. I'll need to explicitly import the SQLPS module since the module auto-loading functionality that was introduced in PowerShell version 3 doesn't work with providers (it doesn't auto-load the module when I try to access the SQL PSDrive, although if I used one of the cmdlets in the module first, the SQL PSDrive would then be available automatically):
<pre class="lang:ps decode:true">Import-Module -Name SQLPS</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-1a.jpg"><img class="alignnone size-full wp-image-11586" src="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-1a.jpg" alt="recoverymodel-1a" width="877" height="97" /></a>

Notice the warning about unapproved verbs in the previous output. There are two cmdlets in the SQLPS module that were created back in the snap-in days (<em>Encode-SqlName</em> and <em>Decode-SqlName</em>). I honestly don't know why they don't fix this since all they need to do is change to approved verbs and create aliases for the old names for backwards compatibility.

Now to query the settings for the ProGet database on SQL02 using the SQL PSProvider:
<pre class="lang:ps decode:true">Get-ChildItem -Path SQLSERVER:\SQL\SQL02\DEFAULT\Databases |
Where-Object Name -eq ProGet</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-2a.jpg"><img class="alignnone size-full wp-image-11588" src="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-2a.jpg" alt="recoverymodel-2a" width="877" height="180" /></a>

As you can see in the previous results, the recovery model of the ProGet database is Full.

Setting the recovery model of a database to Simple is literally simple using the PSProvider:
<pre class="lang:ps decode:true">Get-ChildItem -Path SQLSERVER:\SQL\SQL02\DEFAULT\Databases |
Where-Object Name -eq ProGet |
ForEach-Object {
    $_.RecoveryModel = 'Simple'
    $_.Alter()
    $_.Refresh()
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-3a.jpg"><img class="alignnone size-full wp-image-11589" src="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-3a.jpg" alt="recoverymodel-3a" width="877" height="144" /></a>

The one-liner shown in the previous example is written in such a way that more than one database could be changed by simply modifying the <em>Where-Object</em> portion of the command. Notice that the actual word 'Simple' was used to change the recovery model. It's necessary to call the Alter method to actually apply the changes. The Refresh method is also used so that the changes show up in your current PowerShell session:
<pre class="lang:ps decode:true ">Get-ChildItem -Path SQLSERVER:\SQL\SQL02\DEFAULT\Databases |
Where-Object Name -eq ProGet</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-4a.jpg"><img class="alignnone size-full wp-image-11590" src="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-4a.jpg" alt="recoverymodel-4a" width="877" height="179" /></a>

Now I'll enter a one-to-one remoting session to SQL02 so the commands are actually executed as if I were logged directly into the SQL02 server:
<pre class="lang:ps decode:true">Enter-PSSession -ComputerName SQL02</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-5a.jpg"><img class="alignnone size-full wp-image-11592" src="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-5a.jpg" alt="recoverymodel-5a" width="877" height="72" /></a>

Since SQL02 is running SQL Server 2008 R2, it uses a PowerShell snap-in instead of a module. Before I can use the SQL PSDrive, I'll first need to load the SQL Provider snap-in:
<pre class="lang:ps decode:true ">Add-PSSnapin -Name SqlServerProviderSnapin100 -PassThru</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-6a.jpg"><img class="alignnone size-full wp-image-11593" src="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-6a.jpg" alt="recoverymodel-6a" width="877" height="156" /></a>

A slightly modified version of the same command that I previously ran to check the recovery model of the ProGet database is now run directly on the server:
<pre class="lang:ps decode:true">Get-ChildItem -Path SQLSERVER:\SQL\$env:COMPUTERNAME\DEFAULT\Databases |
Where-Object Name -eq ProGet |
Select-Object -Property Name, RecoveryModel</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-7a.jpg"><img class="alignnone size-full wp-image-11594" src="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-7a.jpg" alt="recoverymodel-7a" width="877" height="167" /></a>

When I try to set the recovery model back to Full using the same syntax that I previously used, it generates an error:
<pre class="lang:ps decode:true">Get-ChildItem -Path SQLSERVER:\SQL\$env:COMPUTERNAME\DEFAULT\Databases |
Where-Object Name -eq ProGet |
ForEach-Object {
    $_.RecoveryModel = 'Full'
    $_.Alter()
    $_.Refresh()
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-8a.jpg"><img class="alignnone size-full wp-image-11595" src="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-8a.jpg" alt="recoverymodel-8a" width="877" height="228" /></a>

<em><span style="color: #ff0000;">Exception setting "RecoveryModel": "Specified cast is not valid."</span></em>
<em> <span style="color: #ff0000;">At line:4 char:5</span></em>
<em> <span style="color: #ff0000;">+ $_.RecoveryModel = 'Full'</span></em>
<em> <span style="color: #ff0000;">+ ~~~~~~~~~~~~~~~~~~~~~~~~~</span></em>
<em> <span style="color: #ff0000;"> + CategoryInfo : NotSpecified: (:) [], SetValueInvocationException</span></em>
<em> <span style="color: #ff0000;"> + FullyQualifiedErrorId : ExceptionWhenSetting</span></em>

When I first tried this, it was on a machine that was still running PowerShell version 2 so I thought that maybe it was a PowerShell version 2 related issue, but that's not the case since this machine is running PowerShell version 4. It appears that the PowerShell snap-in that's included with SQL Server 2008 R2 doesn't have the enumeration to convert the database recovery model name to the corresponding number.

Taking a look at <a href="https://msdn.microsoft.com/en-us/library/ms178534.aspx" target="_blank">MSDN</a>, you'll find what numbers correspond to what database recover model names (1 = Full, 2 = Bulk Logged, and 3 = Simple).

Using the number instead of the name allows the command to complete successfully:
<pre class="lang:ps decode:true">Get-ChildItem -Path SQLSERVER:\SQL\$env:COMPUTERNAME\DEFAULT\Databases |
Where-Object Name -eq ProGet |
ForEach-Object {
    $_.RecoveryModel = 1
    $_.Alter()
    $_.Refresh()
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-9a.jpg"><img class="alignnone size-full wp-image-11596" src="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-9a.jpg" alt="recoverymodel-9a" width="877" height="145" /></a>

As you can see, the database recovery model has been changed back to Full:
<pre class="lang:ps decode:true ">Get-ChildItem -Path SQLSERVER:\SQL\$env:COMPUTERNAME\DEFAULT\Databases |
Where-Object Name -eq ProGet |
Select-Object -Property Name, RecoveryModel</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-10a.jpg"><img class="alignnone size-full wp-image-11597" src="http://mikefrobbins.com/wp-content/uploads/2015/04/recoverymodel-10a.jpg" alt="recoverymodel-10a" width="877" height="168" /></a>

Whether or not this is being accomplished directly on the server or from your workstation doesn't matter, the version of the SQL management tools that is being used to manage the database settings is what matters and if you want to ensure backwards compatibility, use the number that corresponds to the database recovery model instead of the actual name.

µ