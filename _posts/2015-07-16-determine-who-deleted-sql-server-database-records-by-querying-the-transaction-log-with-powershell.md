---
ID: 12024
post_title: >
  Determine who deleted SQL Server
  database records by querying the
  transaction log with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/07/16/determine-who-deleted-sql-server-database-records-by-querying-the-transaction-log-with-powershell/
published: true
post_date: 2015-07-16 07:30:59
---
Have you ever had a scenario where records from one or more SQL server database tables mysteriously came up missing and no one owned up to deleting them? Maybe it was an honest mistake or maybe a scheduled job deleted them. How do you figure out what happened without spending thousands of dollars on a third party product?

You need to determine what happened so this doesn't occur again, but the immediate crisis is to get those records back from a restored copy of the database. How do you determine at what point the records were deleted so point in time recovery of the affected database can be performed into an alternate database so those records can be recovered?

You've got a major crisis on your hands and as always, no budget whatsoever in the form of time or money. You need this fixed now and you need it fixed for little or no cost!
<h1 style="text-align: center;"><strong>PowerShell to the Rescue!</strong></h1>
The following PowerShell function can be used to query the MSDB database on a SQL server instance to retrieve the list of transaction log backup files for a specific database and then query those files and/or the active transaction log for insert, update, or delete operations that have occurred within a specified date and time range.
<pre class="lang:ps decode:true " title="Find-MrSqlDatabaseChange">#Requires -Version 3.0
function Find-MrSqlDatabaseChange {

&lt;#
.SYNOPSIS
    Queries the active transaction log and transaction log backup file(s) for
    insert, update, or delete operations on the specified database.
 
.DESCRIPTION
    Find-MrSqlDatabaseChange is a PowerShell function that is designed to query
    the active transaction log and transaction log backups for either insert,
    update, or delete operations that occurred on the specified database within
    the specified datetime range. The Invoke-MrSqlDataReader function which is
    also part of the MrSQL script module is required.
 
.PARAMETER ServerInstance
    The name of an instance of the SQL Server database engine. For default
    instances, only specify the server name: 'ServerName'. For named instances,
    use the format 'ServerName\InstanceName'.

.PARAMETER TransactionName
    The type of transaction to search for. Valid values are insert, update, or
    delete. The default value is 'Delete'.
 
.PARAMETER Database
    The name of the database to query the transaction log for.

.PARAMETER StartTime
    The beginning datetime to start searching from. The default is at the
    beginning of the current day.

.PARAMETER EndTime
    The ending datetime to stop searching at. The default is at the current
    datetime (now).

.PARAMETER Credential
    SQL Authentication userid and password in the form of a credential object.
 
.EXAMPLE
     Find-MrSqlDatabaseChange -ServerInstance sql04 -Database pubs

.EXAMPLE
     Find-MrSqlDatabaseChange -ServerInstance sql04 -TransactionName Update `
     -Database Northwind -StartTime (Get-Date).AddDays(-14) `
     -EndTime (Get-Date).AddDays(-7) -Credential (Get-Credential)

.EXAMPLE
     'AdventureWorks2012' | Find-MrSqlDatabaseChange -ServerInstance sql02\qa
 
.INPUTS
    String
 
.OUTPUTS
    DataRow
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (        
        [Parameter(Mandatory)]
        [string]$ServerInstance,
        
        [ValidateSet('Insert', 'Update', 'Delete')]
        [string]$TransactionName = 'Delete',

        [Parameter(Mandatory,
                   ValueFromPipeline)]
        [string]$Database,

        [ValidateNotNullOrEmpty()]
        [datetime]$StartTime = (Get-Date).Date,

        [ValidateNotNullOrEmpty()]
        [datetime]$EndTime = (Get-Date),

        [System.Management.Automation.Credential()]$Credential = [System.Management.Automation.PSCredential]::Empty
    )

    BEGIN {
        $Params = @{
            ServerInstance = $ServerInstance
        }
        
        if($PSBoundParameters.Credential) {
            $Params.Credential = $Credential
        }
    }

    PROCESS {
        Write-Verbose -Message "Obtaining a list of transaction log backup files for the $Database database"

        $TransactionLogBackupHistory = Invoke-MrSqlDataReader @Params -Database msdb -Query "
        SELECT backupset.backup_set_id, backupset.last_family_number, backupset.database_name, backupset.recovery_model, backupset.type,
        backupset.position, backupmediafamily.physical_device_name, backupset.backup_start_date, backupset.backup_finish_date
        FROM backupset
        INNER JOIN backupmediafamily
        ON backupset.media_set_id = backupmediafamily.media_set_id
        WHERE database_name = '$Database'
        AND type = 'L'
        AND backup_start_date &gt;= '$StartTime'"

        $TransactionLogBackups = $TransactionLogBackupHistory | Where-Object backup_finish_date -le $EndTime
        $Params.Database = $Database
        
        if (($TransactionLogBackups.count) -ne (($TransactionLogBackups | Select-Object -ExpandProperty backup_set_id -Unique).count)) {
            Write-Verbose -Message 'Transaction log backups were found that are striped accross multiple backup files'

            $UniqueBackupSetId = $TransactionLogBackups | Select-Object -ExpandProperty backup_set_id -Unique
            
            $BackupInfo = foreach ($SetId in $UniqueBackupSetId) {
                Write-Verbose -Message "Creating an updated list of transaction log backup files for backup set $($SetId)"

                $BackupSet = $TransactionLogBackups | Where-Object backup_set_id -in $SetId
                [pscustomobject]@{            
                    backup_set_id = $BackupSet | Select-Object -First 1 -ExpandProperty backup_set_id
                    last_family_number = $BackupSet | Select-Object -First 1 -ExpandProperty last_family_number
                    database_name = $BackupSet | Select-Object -First 1 -ExpandProperty database_name
                    recovery_model = $BackupSet | Select-Object -First 1 -ExpandProperty recovery_model
                    type = $BackupSet | Select-Object -First 1 -ExpandProperty type
                    position = $BackupSet | Select-Object -First 1 -ExpandProperty position
                    physical_device_name = $BackupSet.physical_device_name
                    backup_start_date = $BackupSet | Select-Object -First 1 -ExpandProperty backup_start_date
                    backup_finish_date = $BackupSet | Select-Object -First 1 -ExpandProperty backup_finish_date
                }
            }
        }
        else {
            Write-Verbose -Message 'No transaction log backup sets were found that are striped accross multiple files'
            $BackupInfo = $TransactionLogBackups
        }

        foreach ($Backup in $BackupInfo) {
            Write-Verbose -Message "Building a query to locate the $TransactionName operations in transaction log backup set $($Backup.backup_set_id)"
                            
            $Query = "SELECT [Current LSN], Operation, Context, [Transaction ID], [Transaction Name],
                      Description, [Begin Time], SUser_SName ([Transaction SID]) AS [User]
                      FROM fn_dump_dblog (NULL,NULL,N'DISK',$($Backup.Position),
                      $("N$(($Backup.physical_device_name) | ForEach-Object {"'$_'"})" -replace "' '","', N'"),
                      $((1..(64 - $Backup.last_family_number)) | ForEach-Object {'DEFAULT,'}))
                      WHERE [Transaction Name] = N'$TransactionName'
                      AND [Begin Time] &gt;= '$(($StartTime).ToString('yyyy/MM/dd HH:mm:ss'))'
                      AND ([End Time] &lt;= '$(($EndTime).ToString('yyyy/MM/dd HH:mm:ss'))'
                      OR [End Time] is null)" -replace ',\)',')'

            $Params.Query = $Query

            Write-Verbose -Message "Executing the query for transaction log backup set $($Backup.backup_set_id)"
            Invoke-MrSqlDataReader @Params
        }

        if ($EndTime -gt ($TransactionLogBackupHistory | Select-Object -Last 1 -ExpandProperty backup_finish_date)) {
            Write-Verbose -Message "Building a query to locate the $TransactionName operations in the active transaction log for the $Database database"

            $Query = "SELECT [Current LSN], Operation, Context, [Transaction ID], [Transaction Name],
                      Description, [Begin Time], SUser_SName ([Transaction SID]) AS [User]
                      FROM fn_dblog (NULL, NULL)
                      WHERE [Transaction Name] = N'$TransactionName'
                      AND [Begin Time] &gt;= '$(($StartTime).ToString('yyyy/MM/dd HH:mm:ss'))'                      
                      AND ([End Time] &lt;= '$(($EndTime).ToString('yyyy/MM/dd HH:mm:ss'))'
                      OR [End Time] is null)"
            
            $Params.Query = $Query

            Write-Verbose -Message "Executing the query for the active transaction log for the $Database database"
            Invoke-MrSqlDataReader @Params
        }
    }   
}</pre>
Regardless if you have multiple files with a single transaction log backup in them, multiple transaction log backups in a single file, or transaction log backups striped across multiple files, this function will determine that and dynamically build and execute the necessary query to locate and return the desired transactions.

If the date specified for the <em>EndDate</em> parameter is greater than the end date of the last transaction log backup, then the function automatically knows that it needs to query the active transaction log for those transactions. Oh, and by the way, the query for the transaction log backups is different than the one for the active transaction log but no worries as this function takes care of all of that for you and seamlessly returns the desired results without the user of it ever having to know all of the intricacies of the process itself.
<h1 style="text-align: center;"><strong> That's the POWER of PowerShell!</strong></h1>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/07/bam.jpg"><img class=" size-full wp-image-12045 aligncenter" src="http://mikefrobbins.com/wp-content/uploads/2015/07/bam.jpg" alt="bam" width="300" height="200" /></a>

In the following example, the <em>Find-MrSqlDatabaseChange</em> function is used to find any delete operations that have occurred in the pubs database in the past 14 days:
<pre class="lang:ps decode:true  ">Find-MrSqlDatabaseChange -ServerInstance sql04 -Database pubs -StartTime (Get-Date).AddDays(-14) -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/07/sql-db-changes1a.jpg"><img class="alignnone size-full wp-image-12043" src="http://mikefrobbins.com/wp-content/uploads/2015/07/sql-db-changes1a.jpg" alt="sql-db-changes1a" width="877" height="492" /></a>

As the previous set of results show, there were two deletes that occurred in the pubs database in the past 14 days. Both occurred on July 2nd and both were performed as someone or something that was logged in as the administrator account in the mikefrobbins domain. The <em>"Current LSN"</em> can be used to know what point to restore the database to so the affected database records can be recovered.

The verbose parameter was specified in the previous example so more details about what is occurring as the function runs are displayed, otherwise that additional level of detail wouldn't be present if the verbose parameter was omitted.

Be sure to check out my blog article from last week <em>"<a href="http://mikefrobbins.com/2015/07/09/query-sql-server-from-powershell-without-the-sql-module-or-snapin/" target="_blank">Query SQL Server from PowerShell without the SQL module or snapin</a>"</em> as the <em>Invoke-MrSqlDataReader</em> function that was created and demonstrated in it is required by the <em>Find-MrSqlDatabaseChange</em> function.

The <em>Find-MrSqlDatabaseChange</em> function shown in this blog article can be downloaded as part of my MrSQL PowerShell module from <a href="https://github.com/mikefrobbins/SQL" target="_blank">GitHub</a>.

µ