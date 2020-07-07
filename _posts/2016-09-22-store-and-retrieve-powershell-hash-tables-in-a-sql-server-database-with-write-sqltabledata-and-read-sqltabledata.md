---
ID: 14529
post_title: >
  Store and Retrieve PowerShell Hash
  Tables in a SQL Server Database with
  Write-SqlTableData and Read-SqlTableData
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/09/22/store-and-retrieve-powershell-hash-tables-in-a-sql-server-database-with-write-sqltabledata-and-read-sqltabledata/
published: true
post_date: 2016-09-22 07:30:42
---
In <a href="http://mikefrobbins.com/2016/09/15/store-environmental-code-in-a-sql-server-database-for-powershell-operational-validation-tests/" target="_blank">my blog article from last week</a>, I demonstrated using several older open source PowerShell functions to store the environmental portion of the code from operational validation tests in a SQL Server database and then later retrieve it and re-hydrate it back into a PowerShell hash table.

Earlier this week, a new release of the SQLServer PowerShell module was released as <a href="https://msdn.microsoft.com/en-us/library/mt238290.aspx" target="_blank">part of SSMS (SQL Server Management Studio)</a>:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/sqlserver164-1a.png"><img class="alignnone size-full wp-image-14531" src="http://mikefrobbins.com/wp-content/uploads/2016/09/sqlserver164-1a.png" alt="sqlserver164-1a" width="695" height="600" /></a>

It includes three new cmdlets, two of which can be used to store and retrieve data in a SQL Server database from PowerShell instead of the older open source ones that I demonstrated in the previously referenced blog article from last week.
<pre class="lang:ps decode:true ">Get-Command -Module SQLServer -Name *Sql*Data</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/sqlserver164-7a.png"><img class="alignnone size-full wp-image-14541" src="http://mikefrobbins.com/wp-content/uploads/2016/09/sqlserver164-7a.png" alt="sqlserver164-7a" width="859" height="154" /></a>

A array of PowerShell hash tables have been stored in a variable named ConfigData:
<pre class="lang:ps decode:true ">$ConfigData = @(
        @{
            Environment = 'Production'
            RtServiceName = '*Runtime Server'
            RtPort = 9750
            RtSSLPort = 9760
            AdmServiceName = '*System Admin Server'
            AdmPort = 1234
            AdmSSLPort = 1235
        }
        @{
            Environment = 'Training'
            RtServiceName = '*Runtime Server - Train'
            RtPort = 9850
            RtSSLPort = 9860
            AdmServiceName = '*System Admin Server - Train'
            AdmPort = 4234
            AdmSSLPort = 4235
        }
        @{
            Environment = 'Test'
            RtServiceName = '*Runtime Server - Test'
            RtPort = 9950
            RtSSLPort = 9960
            AdmServiceName = '*System Admin Server - Test'
            AdmPort = 3234
            AdmSSLPort = 3235
        }
        @{
            Environment = 'Report'
            RtServiceName = '*Runtime Server - Report'
            RtPort = 9650
            RtSSLPort = 9660
            AdmServiceName = '*System Admin Server - Report'
            AdmPort = 2234
            AdmSSLPort = 2235
        }
)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/sqlserver164-2a.png"><img class="alignnone size-full wp-image-14532" src="http://mikefrobbins.com/wp-content/uploads/2016/09/sqlserver164-2a.png" alt="sqlserver164-2a" width="859" height="494" /></a>

I prefer to convert the hash tables to PSCustomObjects before storing the information in the database because the data seems more natural in the database and the results of a simple database query are cleaner.

Several of the examples shown in this blog article require PowerShell version 4 or higher since I’ve chosen to use the ForEach method.
<pre class="lang:ps decode:true">$ConfigData.ForEach({$_.ForEach({[PSCustomObject]$_})}) |
Format-Table

$ConfigData.ForEach({$_.ForEach({[PSCustomObject]$_})}) |
Get-Member</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/sqlserver164-3a.png"><img class="alignnone size-full wp-image-14533" src="http://mikefrobbins.com/wp-content/uploads/2016/09/sqlserver164-3a.png" alt="sqlserver164-3a" width="859" height="207" /></a>

Store a few things in variables so they don’t have to be continually reference statically:
<pre class="lang:ps decode:true">$Instance = 'sql01'
$Database = 'PSConfigData'
$Table = 'AppServer'</pre>
The SQL Server database already exists, but not the table. The Force parameter creates the table automatically:
<pre class="lang:ps decode:true">$ConfigData.ForEach({$_.ForEach({[PSCustomObject]$_}) |
Write-SqlTableData -ServerInstance $Instance -DatabaseName $Database -SchemaName dbo -TableName $Table -Force})</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/sqlserver164-4a.png"><img class="alignnone size-full wp-image-14534" src="http://mikefrobbins.com/wp-content/uploads/2016/09/sqlserver164-4a.png" alt="sqlserver164-4a" width="859" height="73" /></a>

Read the data from SQL Server to verify that it was indeed written to the database:
<pre class="lang:ps decode:true">Read-SqlTableData -ServerInstance $Instance -TableName $Table -DatabaseName $Database -SchemaName dbo |
Format-Table -AutoSize

Read-SqlTableData -ServerInstance $Instance -TableName $Table -DatabaseName $Database -SchemaName dbo |
Get-Member</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/sqlserver164-5b.png"><img class="alignnone size-full wp-image-14536" src="http://mikefrobbins.com/wp-content/uploads/2016/09/sqlserver164-5b.png" alt="sqlserver164-5b" width="859" height="228" /></a>

Use my <em>ConvertTo-MrHashTable</em> function (which can be downloaded from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository on GitHub</a>) to convert the data retrieved from SQL Server back into a hash table:
<pre class="lang:ps decode:true">Read-SqlTableData -ServerInstance $Instance -TableName $Table -DatabaseName $Database -SchemaName dbo |
ConvertTo-MrHashTable</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/sqlserver164-6a.png"><img class="alignnone size-full wp-image-14537" src="http://mikefrobbins.com/wp-content/uploads/2016/09/sqlserver164-6a.png" alt="sqlserver164-6a" width="859" height="518" /></a>

The nice thing about these cmdlets is they're native to the latest version of the SQLServer PowerShell module.

µ