---
ID: 13735
post_title: >
  Converting a SQL Server Log Sequence
  Number with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/03/31/converting-a-sql-server-log-sequence-number-with-powershell/
published: true
post_date: 2016-03-31 07:30:27
---
As demonstrated in one of my previous blog articles "<em><a href="http://mikefrobbins.com/2015/07/16/determine-who-deleted-sql-server-database-records-by-querying-the-transaction-log-with-powershell/" target="_blank">Determine who deleted SQL Server database records by querying the transaction log with PowerShell</a></em>", someone or something has deleted records from a SQL Server database. You've used my <em>Find-MrSqlDatabaseChange</em> function to determine when the delete operation occurred based on information contained in either transaction log backups or the active transaction log:
<pre class="lang:ps decode:true">Find-MrSqlDatabaseChange -ServerInstance SQL01 -Database pubs -StartTime (Get-Date -Date '03/28/2016 14:55 PM')</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/convert-lsn1a.png" rel="attachment wp-att-13737"><img class="alignnone size-full wp-image-13737" src="http://mikefrobbins.com/wp-content/uploads/2016/03/convert-lsn1a.png" alt="convert-lsn1a" width="859" height="214" /></a>

You're ready to perform point in time recovery of the database to the LSN (Log Sequence Number) just before the delete occurred but the LSN provided from the transaction log is in a different format than what's required to perform a restore of the database. How can the "<em>Current LSN</em>" as shown in the previous set of results be converted to what's needed to perform point in time recovery of the database? You could use some cryptic T-SQL code, but a PowerShell function can also be written as a reusable tool and added to your SQL toolkit:
<pre class="lang:ps decode:true " title="Convert-MrSqlLogSequenceNumber">#Requires -Version 3.0
function Convert-MrSqlLogSequenceNumber {

&lt;#
.SYNOPSIS
    Converts a SQL LSN (log sequence number) from a three part hexadecimal number
    format to a decimal based string format.
 
.DESCRIPTION
    Convert-MrSqlLogSequenceNumber is an advanced PowerShell function that converts
    one or more SQL transaction log sequence numbers from a three part hexadecimal
    format that is obtained from querying a transaction log backup or the active
    transaction log to a decimal based string format which can be used to perform
    point in time recovery of a SQL Server database using the StopAtMark option.
 
.PARAMETER LogSequenceNumber
    The three part hexadecimal SQL log sequence number obtained from an insert, update,
    or delete operation that has been recorded in the transaction log or log backup.
 
.EXAMPLE
     Convert-MrSqlLogSequenceNumber -LogSequenceNumber '0000002e:00000158:0001'

.EXAMPLE
     '0000002e:00000158:0001' | Convert-MrSqlLogSequenceNumber

.INPUTS
    String
 
.OUTPUTS
    PSCustomObject
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [Parameter(Mandatory,
                   ValueFromPipeline,
                   ValueFromPipelineByPropertyName)]
        [ValidateScript({
          If ($_ -match '^[0-9A-Fa-f]+:[0-9A-Fa-f]+:[0-9A-Fa-f]+$') {
            $True
          }
          else {
            Throw "$_ is not a valid three part hexadecimal SQL log sequence number from a transaction log backup."
          }
        })]
        [Alias('LSN', 'Current LSN')]
        [string[]]$LogSequenceNumber
    )
    
    PROCESS {
        
        foreach ($Number in $LogSequenceNumber) {
            $i = 1

            $Results = foreach ($n in $Number -split ':') {
                $Int = [convert]::ToInt32($n, 16)
                switch ($i) {
                    1 {$Int; break}
                    2 {"$([string]0*(10-(([string]$Int).Length)))$Int"; break}
                    3 {"$([string]0*(5-(([string]$Int).Length)))$Int"; break}
                    Default {Throw 'An unexpected error has occured.'}
                }
                $i++
            }

            [pscustomobject]@{
                'OriginalLSN' = $Number
                'ConvertedLSN' = $Results -join ''
            }            

        }

    }

}</pre>
Take a moment to notice how the code in the previous example is formatted. It's formatted for readability. Your co-workers and future self will thank you. Also notice that it contains a "<em>Requires</em>" statement which states the minimum version of PowerShell that's required. It includes comment based help so help can be obtained just like it is for any other cmdlet. It uses parameter validation to require that a value is provided and that it's in a specific format. All of these are standard items that should be included in any PowerShell function &lt;period&gt;.

The conversion returns both the original LSN and the converted LSN:
<pre class="lang:ps decode:true ">Convert-MrSqlLogSequenceNumber -LSN 0000002e:00000158:0001</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/convert-lsn2a.png" rel="attachment wp-att-13738"><img class="alignnone size-full wp-image-13738" src="http://mikefrobbins.com/wp-content/uploads/2016/03/convert-lsn2a.png" alt="convert-lsn2a" width="859" height="133" /></a>

An even better option is to make PowerShell tools modular so the <em>Convert-MrSqlLogSequenceNumber</em> function can accept the output of my previously created <em>Find-MrSqlDatabaseChange</em> function via pipeline input so the necessary log sequence number can be determined with a single PowerShell one-liner instead of having to run one command and then copy and paste its output as input for the next command:
<pre class="lang:ps decode:true">Find-MrSqlDatabaseChange -ServerInstance SQL01 -Database pubs -StartTime (Get-Date -Date '03/28/2016 14:55 PM') |
Convert-MrSqlLogSequenceNumber</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/convert-lsn3a.png" rel="attachment wp-att-13741"><img class="alignnone size-full wp-image-13741" src="http://mikefrobbins.com/wp-content/uploads/2016/03/convert-lsn3a.png" alt="convert-lsn3a" width="859" height="144" /></a>

Maybe there are multiple log sequence numbers that need translating? That functionality is built in as well:
<pre class="lang:ps decode:true ">Find-MrSqlDatabaseChange -ServerInstance SQL01 -Database pubs -StartTime (Get-Date -Date '03/01/2016 14:55 PM') |
Convert-MrSqlLogSequenceNumber</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/03/convert-lsn4a.png" rel="attachment wp-att-13742"><img class="alignnone size-full wp-image-13742" src="http://mikefrobbins.com/wp-content/uploads/2016/03/convert-lsn4a.png" alt="convert-lsn4a" width="859" height="167" /></a>

All of the PowerShell functions shown and/or referenced in this blog article can be downloaded from <a href="http://github.com/mikefrobbins/SQL" target="_blank">my SQL repository on GitHub</a>.

If you've found this blog article interesting and you're attending the <em><a href="http://powershellsummit.org" target="_blank">PowerShell and DevOps Global Summit 2016</a></em> next week, then you should definitely consider attending my "<em>Building Unconventional SQL Server Tools in PowerShell with Advanced Functions and Script Modules</em>" session on Wednesday afternoon, April 6th.

µ