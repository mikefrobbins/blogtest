---
ID: 11982
post_title: >
  Query SQL Server from PowerShell without
  the SQL module or snapin
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/07/09/query-sql-server-from-powershell-without-the-sql-module-or-snapin/
published: true
post_date: 2015-07-09 07:30:30
---
There are several different ways to query a SQL Server from PowerShell but most often you'll find that they're dependent on the SQL PowerShell module or snapin.

To eliminate that dependency, you can query a SQL Server from PowerShell using the .NET framework. There are several different options to accomplish this including using a DataReader or a DataSet and there are plenty of tutorials on those topics already on the Internet so there's no reason to duplicate that information here. Most of the tutorials are developer focused, but you should be able to translate them to PowerShell easy enough.

The PowerShell function shown in the following example uses a DataReader along with a DataTable to retrieve the information from SQL Server and display it in PowerShell. One Transact-SQL (TSQL) select statement can be piped into this function or provided via parameter input. The function also includes comment based help and error handling. Either a trusted database connection or SQL authentication can be used.
<pre class="lang:ps decode:true" title="Invoke-MrSqlDataReader">#Requires -Version 3.0
function Invoke-MrSqlDataReader {

&lt;#
.SYNOPSIS
    Runs a select statement query against a SQL Server database.
 
.DESCRIPTION
    Invoke-MrSqlDataReader is a PowerShell function that is designed to query
    a SQL Server database using a select statement without the need for the SQL
    PowerShell module or snap-in being installed.
 
.PARAMETER ServerInstance
    The name of an instance of the SQL Server database engine. For default instances,
    only specify the server name: 'ServerName'. For named instances, use the format
    'ServerName\InstanceName'.
 
.PARAMETER Database
    The name of the database to query on the specified SQL Server instance.
 
.PARAMETER Query
    Specifies one Transact-SQL select statement query to be run.

.PARAMETER Credential
    SQL Authentication userid and password in the form of a credential object.
 
.EXAMPLE
     Invoke-MrSqlDataReader -ServerInstance Server01 -Database Master -Query '
     select name, database_id, compatibility_level, recovery_model_desc from sys.databases'

.EXAMPLE
     'select name, database_id, compatibility_level, recovery_model_desc from sys.databases' |
     Invoke-MrSqlDataReader -ServerInstance Server01 -Database Master

.EXAMPLE
     'select name, database_id, compatibility_level, recovery_model_desc from sys.databases' |
     Invoke-MrSqlDataReader -ServerInstance Server01 -Database Master -Credential (Get-Credential)
 
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

        [Parameter(Mandatory)]
        [string]$Database,
        
        [Parameter(Mandatory,
                   ValueFromPipeline)]
        [string]$Query,
        
        [System.Management.Automation.Credential()]$Credential = [System.Management.Automation.PSCredential]::Empty
    )
    
    BEGIN {
        $connection = New-Object -TypeName System.Data.SqlClient.SqlConnection

        if (-not($PSBoundParameters.Credential)) {
            $connectionString = "Server=$ServerInstance;Database=$Database;Integrated Security=True;"
        }
        else {
            $connectionString = "Server=$ServerInstance;Database=$Database;Integrated Security=False;"
            $userid= $Credential.UserName -replace '^.*\\|@.*$'
            ($password = $credential.Password).MakeReadOnly()
            $sqlCred = New-Object -TypeName System.Data.SqlClient.SqlCredential($userid, $password)
            $connection.Credential = $sqlCred
        }

        $connection.ConnectionString = $connectionString
        $ErrorActionPreference = 'Stop'
        
        try {
            $connection.Open()
            Write-Verbose -Message "Connection to the $($connection.Database) database on $($connection.DataSource) has been successfully opened."
        }
        catch {
            Write-Error -Message "An error has occurred. Error details: $($_.Exception.Message)"
        }
        
        $ErrorActionPreference = 'Continue'
        $command = $connection.CreateCommand()
    }

    PROCESS {
        $command.CommandText = $Query
        $ErrorActionPreference = 'Stop'

        try {
            $result = $command.ExecuteReader()
        }
        catch {
            Write-Error -Message "An error has occured. Error Details: $($_.Exception.Message)"
        }

        $ErrorActionPreference = 'Continue'

        if ($result) {
            $dataTable = New-Object -TypeName System.Data.DataTable
            $dataTable.Load($result)
            $dataTable
        }
    }

    END {
        $connection.Close()
    }

}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/powershell-sql-datareader.jpg"><img class="alignnone size-full wp-image-11984" src="http://mikefrobbins.com/wp-content/uploads/2015/06/powershell-sql-datareader.jpg" alt="powershell-sql-datareader" width="877" height="250" /></a>

For more information and options on querying SQL Server using the .NET framework, see the <a href="https://msdn.microsoft.com/en-us/library/ms254931(v=vs.110).aspx" target="_blank">DataAdapters and DataReaders</a>, <a href="https://msdn.microsoft.com/en-us/library/System.Data.DataTable(v=vs.110).aspx" target="_blank">DataTable Class</a>, and <a href="https://msdn.microsoft.com/en-us/library/system.data.dataset(v=vs.110).aspx" target="_blank">DataSet Class</a> topics on MSDN.

The <em>Invoke-MrSqlDataReader</em> PowerShell function shown in this blog article can be downloaded as part of my MrSQL PowerShell module on <a href="https://github.com/mikefrobbins/SQL" target="_blank">GitHub</a>.

µ