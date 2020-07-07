---
ID: 15039
post_title: >
  PowerShell One-Liner to Disable Active
  Directory Accounts and Log the Results
  to a SQL Server Database
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/02/16/powershell-one-liner-to-disable-active-directory-accounts-and-log-the-results-to-a-sql-server-database/
published: true
post_date: 2017-02-16 07:30:51
---
The new PowerShell cmdlets that are part of the SQLServer PowerShell module that's distributed as part of <a href="https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms" target="_blank">SSMS (SQL Server Management Studio) 2016</a> make it super easy to write the output of PowerShell commands to a SQL Server database.

The ActiveDirectory PowerShell module that's part of the <a href="https://www.microsoft.com/en-us/download/details.aspx?id=45520" target="_blank">RSAT (Remote Server Administration Tools)</a> is also required by the code shown in this blog article.

This PowerShell one-liner retrieves a list of Active Directory users who have not logged in within the past 120 days, are enabled, and exist in the Adventure Works OU (Organizational Unit). It disables them and logs the results to a table in a SQL Server database. Note that the <em>LastLogonTimeStamp</em> property can be off by up to 14 days so be sure to take that into account. See <a href="https://blogs.technet.microsoft.com/askds/2009/04/15/the-lastlogontimestamp-attribute-what-it-was-designed-for-and-how-it-works/" target="_blank">this Active Directory team article</a> for more information about that particular property.

Keep in mind that a PowerShell one-liner is one continuous pipeline and not necessarily a command that's on one physical line. Not all commands that are on one physical line are one-liners.
<pre class="lang:ps decode:true">(Get-Date).AddDays(-120) |
ForEach-Object {
Get-ADUser -Filter {
    LastLogonTimeStamp -lt $_ -and enabled -eq $true
} -SearchBase 'OU=AdventureWorks Users,OU=Users,OU=Test,DC=mikefrobbins,DC=com' -Properties LastLogonTimeStamp -PipelineVariable UserInfo} |
Disable-ADAccount -PassThru |
Select-Object -Property Name, SamAccountName,
                        @{label='LastLogon';expression={[DateTime]::FromFileTime($UserInfo.LastLogonTimestamp).ToString('yyyy-MM-dd HH:mm:ss')}},
                        @{label='DisabledDate';expression={(Get-Date).ToString('yyyy-MM-dd HH:mm:ss')}} |
Write-SqlTableData -ServerInstance sql011 -DatabaseName MrOps -SchemaName dbo -TableName DisabledAdUsers -Force</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/02/logresultstosql1c.png"><img class="alignnone size-full wp-image-15047" src="http://mikefrobbins.com/wp-content/uploads/2017/02/logresultstosql1c.png" alt="" width="859" height="191" /></a>

Using <em>Write-SqlTableData</em> with the <em>Force</em> parameter creates the database and table on the SQL Server if they don't already exist.

Retrieving the results is as simple as querying the SQL Server database:
<pre class="lang:ps decode:true ">Invoke-Sqlcmd -ServerInstance sql011 -Database MrOps -Query 'select * from DisabledAdUsers'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/02/logresultstosql2c.png"><img class="alignnone size-full wp-image-15049" src="http://mikefrobbins.com/wp-content/uploads/2017/02/logresultstosql2c.png" alt="" width="859" height="154" /></a>

Now you know exactly when the accounts were disabled instead of trying to depending on the modified date in Active Directory and not knowing if disabling the account was the last modification that took place or not.

Never underestimate the power of a PowerShell one-liner. PowerShell one-liners will be covered in more detail in <a href="https://leanpub.com/powershell101" target="_blank">my new PowerShell 101 book</a>.

µ