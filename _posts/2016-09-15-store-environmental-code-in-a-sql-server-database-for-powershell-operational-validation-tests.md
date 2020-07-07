---
ID: 14484
post_title: >
  Store Environmental Code in a SQL Server
  Database for PowerShell Operational
  Validation Tests
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/09/15/store-environmental-code-in-a-sql-server-database-for-powershell-operational-validation-tests/
published: true
post_date: 2016-09-15 07:30:50
---
I've previously published articles on separating environmental code from structural code for both DSC (Desired State Configuration) and Operational Validation or Readiness Tests. This article picks up where I left off last week in "<a href="http://mikefrobbins.com/2016/09/08/separating-environmental-code-from-structural-code-in-powershell-operational-validation-tests/" target="_blank">Separating Environmental Code from Structural Code in PowerShell Operational Validation Tests</a>".

As many existing open source PowerShell functions as possible have been used in the examples shown in this blog article instead of re-inventing the wheel and rewriting everything from scratch. All of these open-source functions have been added to <a href="https://github.com/mikefrobbins/SQL" target="_blank">my SQL repository on GitHub</a>. Also note that several of the examples shown in this blog article require PowerShell version 4 or higher since I've chosen to use the ForEach method.

I'll start out with the environmental code that I used in the previously referenced blog article from last week:
<pre class="lang:ps decode:true">$ConfigData = @(

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
Store a few things in variables that I don't want to have to continually reference statically:
<pre class="lang:ps decode:true ">$Instance = 'sql01'
$Database = 'PSConfigData'
$Table = 'AppServer'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/envcode-sql1a.png"><img class="alignnone size-full wp-image-14488" src="http://mikefrobbins.com/wp-content/uploads/2016/09/envcode-sql1a.png" alt="envcode-sql1a" width="859" height="87" /></a>

Create a SQL Server database named PSConfigData:
<pre class="lang:ps decode:true ">Invoke-Sqlcmd2 -ServerInstance $Instance -Database master -QueryTimeout 10 -Query "
    Create Database $Database
"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/envcode-sql2a.png"><img class="alignnone size-full wp-image-14489" src="http://mikefrobbins.com/wp-content/uploads/2016/09/envcode-sql2a.png" alt="envcode-sql2a" width="859" height="85" /></a>

The next command is a little tricky because $ConfigData contains an array of hash tables. Create an array of data tables from the hash tables stored in the $ConfigData variable:
<pre class="lang:ps decode:true ">$DataTable = $ConfigData.ForEach({$_.ForEach({[PSCustomObject]$_}) | Out-DataTable})</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/envcode-sql3a.png"><img class="alignnone size-full wp-image-14490" src="http://mikefrobbins.com/wp-content/uploads/2016/09/envcode-sql3a.png" alt="envcode-sql3a" width="859" height="61" /></a>

Create a table named AppServer in the SQL Server database. Since the $DataTable variable contains an array of data tables, only one of them should be specified when creating the actual sql table:
<pre class="lang:ps decode:true ">Add-SqlTable -ServerInstance $Instance -Database $Database -TableName $Table -DataTable $DataTable[0]</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/envcode-sql4a.png"><img class="alignnone size-full wp-image-14491" src="http://mikefrobbins.com/wp-content/uploads/2016/09/envcode-sql4a.png" alt="envcode-sql4a" width="859" height="58" /></a>

Write the data from each of the data tables stored in the $DataTable variable to the newly created SQL Server database table:
<pre class="lang:ps decode:true">$DataTable.ForEach({Write-DataTable -ServerInstance $Instance -Database $Database -TableName $Table -Data $_ -QueryTimeout 10})</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/envcode-sql5a.png"><img class="alignnone size-full wp-image-14492" src="http://mikefrobbins.com/wp-content/uploads/2016/09/envcode-sql5a.png" alt="envcode-sql5a" width="859" height="71" /></a>

Query the previously inserted data from the SQL Server database to verify that it does indeed exist in the table:
<pre class="lang:ps decode:true">Invoke-Sqlcmd2 -ServerInstance $Instance -Database $Database -Query "SELECT * FROM $Table" | Format-Table -AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/envcode-sql6a.png"><img class="alignnone size-full wp-image-14493" src="http://mikefrobbins.com/wp-content/uploads/2016/09/envcode-sql6a.png" alt="envcode-sql6a" width="859" height="180" /></a>

None of this would be useful unless the environmental data previously stored in the SQL Server database could be re-hydrated and turned back into a PowerShell hash table.

I've created a reusable function named <em>ConvertTo-MrHashTable</em> to turn objects into a hash table. This function can be downloaded from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository on GitHub</a>.
<pre class="lang:ps decode:true " title="ConvertTo-MrHashTable">function ConvertTo-MrHashTable {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory,
                   ValueFromPipeline)]
        [PSObject[]]$Object
    )
    PROCESS {
        foreach ($o in $Object) {
            $hashtable = @{}
            
            foreach ($p in Get-Member -InputObject $o -MemberType Property) {
                $hashtable.($p.Name) = $o.($p.Name)
            }

            Write-Output $hashtable
        }
    }
}</pre>
Simply pipe the previous command to the <em>ConvertTo-MrHashTable</em> function to turn the results into a hash table:
<pre class="lang:ps decode:true ">Invoke-Sqlcmd2 -ServerInstance $Instance -Database $Database -Query "SELECT * FROM $Table" |
ConvertTo-MrHashtable</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/envcode-sql7a.png"><img class="alignnone size-full wp-image-14503" src="http://mikefrobbins.com/wp-content/uploads/2016/09/envcode-sql7a.png" alt="envcode-sql7a" width="859" height="466" /></a>

I'll use the same operational validation test from the previous referenced blog article from last week where I separated the environmental code from the structural code except this time I'll retrieve the environmental portion of the code from the SQL Server database:
<pre class="lang:ps decode:true">Test-MrAppServer -ComputerName Server01, Server02 -ConfigurationData (
    Invoke-Sqlcmd2 -ServerInstance $Instance -Database $Database -Query "SELECT * FROM $Table" |
    ConvertTo-MrHashtable
)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/envcode-sql8a.png"><img class="alignnone size-full wp-image-14519" src="http://mikefrobbins.com/wp-content/uploads/2016/09/envcode-sql8a.png" alt="envcode-sql8a" width="859" height="632" /></a>

Since I haven't seen anyone else take the approach of storing their environmental code in a database, I'd like to know what your thoughts are and if you can think of any problems that I might not have considered.

µ