---
ID: 17759
post_title: >
  Run SQL Server PowerShell Cmdlets as a
  Different User
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/04/11/run-sql-server-powershell-cmdlets-as-a-different-user/
published: true
post_date: 2019-04-11 07:30:04
---
One of the ways I practice the principal of least privilege is by logging into my computer as a domain user who is also a standard local user. I typically run PowerShell as a domain user who is a local admin and elevate on a per command basis using a domain account with more access only when absolutely necessary. The problem I've run into is neither the account I'm logged into my computer as or the one I'm running PowerShell as has the ability to execute SQL queries that I need to run against various SQL servers in my environment.

I've installed both the SQLServer and BetterCredentials PowerShell modules from the PowerShell Gallery which are used thoughout this blog article. Windows PowerShell version 5.0 or higher (which ships by default with Windows 10) is required to run some of the commands shown in this blog article.
<pre class="lang:ps decode:true">Install-Module -Name SqlServer, BetterCredentials -AllowClobber</pre>
I don't want to run PowerShell with elevated domain credentials &lt;period&gt;. Unless I'm missing something, the <em>Credential</em> parameter for all of the various SQL cmdlets seems to want SQL authentication credentials, not Windows authentication ones.

At first, I tried kicking off another PowerShell process from within PowerShell as an elevated user to run the SQL commands from. The problem is the process started, ran, returned the results, and exited when completed.
<pre class="lang:ps decode:true ">Start-Process -FilePath "$env:windir\System32\WindowsPowerShell\v1.0\powershell.exe" -ArgumentList "
    Invoke-Sqlcmd -ServerInstance sql16 -Database master -Query '
        select * from sys.databases'
" -Credential (Get-Credential -UserName mikefrobbins\administrator -Store)</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/04/sql-alt-user1a.jpg"><img class="alignnone size-full wp-image-17761" src="https://mikefrobbins.com/wp-content/uploads/2019/04/sql-alt-user1a.jpg" alt="" width="859" height="103" /></a>

<em>Start-Process</em> also has a <em>Wait</em> parameter so I tried it to see if it would cause the PowerShell process that's spawned to wait until it was manually closed. No such luck as it also exited automatically when completed.
<pre class="lang:ps decode:true">Start-Process -FilePath "$env:windir\System32\WindowsPowerShell\v1.0\powershell.exe" -ArgumentList "
    Invoke-Sqlcmd -ServerInstance sql16 -Database master -Query '
        select * from sys.databases'
" -Credential (Get-Credential -UserName mikefrobbins\administrator -Store) -Wait</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/04/sql-alt-user2a.jpg"><img class="alignnone size-full wp-image-17762" src="https://mikefrobbins.com/wp-content/uploads/2019/04/sql-alt-user2a.jpg" alt="" width="859" height="102" /></a>

The only way to make this option work is to pipe the results to <em>Out-GridView</em> with its <em>Wait</em> parameter. This keeps the spawned PowerShell process open along with the grid view window until it's manually closed.
<pre class="lang:ps decode:true">Start-Process -FilePath "$env:windir\System32\WindowsPowerShell\v1.0\powershell.exe" -ArgumentList "
    Invoke-Sqlcmd -ServerInstance sql16 -Database master -Query '
        select * from sys.databases' |
    Out-GridView -Wait
" -Credential (Get-Credential -UserName mikefrobbins\administrator -Store)</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/04/sql-alt-user3a.jpg"><img class="alignnone size-full wp-image-17763" src="https://mikefrobbins.com/wp-content/uploads/2019/04/sql-alt-user3a.jpg" alt="" width="859" height="350" /></a>

A better option is to simply run the command as a PowerShell job using the <em>Start-Job</em> cmdlet since <em>Invoke-Sqlcmd</em> doesn't have an <em>AsJob</em> parameter.
<pre class="lang:ps decode:true">Start-Job -ScriptBlock {
    Invoke-Sqlcmd -ServerInstance sql16 -Database master -Query '
    select * from sys.databases'
} -Credential (Get-Credential -UserName mikefrobbins\administrator -Store)</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/04/sql-alt-user4a.jpg"><img class="alignnone size-full wp-image-17764" src="https://mikefrobbins.com/wp-content/uploads/2019/04/sql-alt-user4a.jpg" alt="" width="859" height="186" /></a>

<em>Get-Job</em> is used to determine the state of the job. As you can see in the following results, the job has completed.
<pre class="lang:ps decode:true">Get-Job -Name Job1</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/04/sql-alt-user5a.jpg"><img class="alignnone size-full wp-image-17765" src="https://mikefrobbins.com/wp-content/uploads/2019/04/sql-alt-user5a.jpg" alt="" width="859" height="144" /></a>

<em>Receive-Job</em> is used to return the results of the job. Note, the first time the <em>Keep</em> parameter is not specified, the results will be removed from the job.
<pre class="lang:ps decode:true ">Get-Job -Name Job1 | Receive-Job -Keep | Format-Table</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/04/sql-alt-user6a.jpg"><img class="alignnone size-full wp-image-17766" src="https://mikefrobbins.com/wp-content/uploads/2019/04/sql-alt-user6a.jpg" alt="" width="859" height="201" /></a>

<em>Remove-Job</em> is used to remove the actual job.
<pre class="lang:ps decode:true">Get-Job -Name Job1 | Remove-Job</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/04/sql-alt-user7a.jpg"><img class="alignnone size-full wp-image-17767" src="https://mikefrobbins.com/wp-content/uploads/2019/04/sql-alt-user7a.jpg" alt="" width="859" height="59" /></a>

If there's a different way to accomplish this task, I'd love to hear about it via a comment to this blog article.

µ