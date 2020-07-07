---
ID: 14717
post_title: >
  Start a SQL Agent Job with the .NET
  Framework from PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/11/22/start-a-sql-agent-job-with-the-net-framework-from-powershell/
published: true
post_date: 2016-11-22 07:30:46
---
As of this writing, the most recent version of the SQLServer PowerShell module (which installs as part of <a href="https://msdn.microsoft.com/en-us/library/mt238290.aspx" target="_blank">SQL Server Management Studio</a>) includes cmdlets for retrieving information about SQL agent jobs, but no cmdlets for starting them.
<pre class="lang:ps decode:true ">Get-Command -Module SQLServer -Name *job*</pre>
<img class="alignnone size-full wp-image-14720" src="http://mikefrobbins.com/wp-content/uploads/2016/11/startsqljob1a.png" alt="startsqljob1a" width="859" height="165" />

I recently ran into a situation where I needed to start a SQL agent job from PowerShell. The solution needed to be a tool that others could use who may or may not have the SQLServer module, SQLPS module or older SQL Server snap-in installed.

I decided to write a function to leverage the .NET Framework from PowerShell to start a SQL Server job:
<pre class="lang:ps decode:true " title="Start-MrSqlAgentJob">#Requires -Version 3.0
function Start-MrSqlAgentJob {

&lt;#
.SYNOPSIS
    Starts the specified SQL Agent Job on the specified target instance of SQL Server.
 
.DESCRIPTION
    Start-MrSqlAgentJob is a PowerShell function that is designed to start the specified SQL Server
    Agent job on the specified target instance of SQL Server without requiring the SQL Server PowerShell
    module or snap-in to be installed.
 
.PARAMETER ServerInstance
    The name of an instance of SQL Server where the SQL Agent is running. For default instances, only
    specify the computer name: MyComputer. For named instances, use the format ComputerName\InstanceName.
 
.PARAMETER Name
    Specifies the name of the Job object that this cmdlet gets. The name may or may not be
    case-sensitive, depending on the collation of the SQL Server where the SQL Agent is running.

.PARAMETER Credential
    SQL Authentication userid and password in the form of a credential object.
 
.EXAMPLE
     Start-MrSqlAgentJob -ServerInstance SQLServer01 -Name syspolicy_purge_history

.EXAMPLE
     Start-MrSqlAgentJob -ServerInstance SQLServer01 -Name syspolicy_purge_history -Credential (Get-Credential)

.EXAMPLE
     'syspolicy_purge_history' | Start-MrSqlAgentJob -ServerInstance SQLServer01
 
.INPUTS
    String
 
.OUTPUTS
    Boolean
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (        
        [Parameter(Mandatory)]
        [string]$ServerInstance,

        [Parameter(Mandatory,
                   ValueFromPipeLine)]
        [string]$Name,
        
        [System.Management.Automation.Credential()]$Credential = [System.Management.Automation.PSCredential]::Empty
    )
    
    BEGIN {

        $Database = 'msdb'
        $connection = New-Object -TypeName System.Data.SqlClient.SqlConnection

        if (-not($PSBoundParameters.Credential)) {
            $connectionString = "Server=$ServerInstance;Database=$Database;Integrated Security=True;"
        }
        else {
            $connectionString = "Server=$ServerInstance;Database=$Database;Integrated Security=False;"
            $userid = $Credential.UserName -replace '^.*\\|@.*$'
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

        $Query = "EXEC dbo.sp_start_job N'$Name'"        
        $command.CommandText = $Query
        $ErrorActionPreference = 'Stop'

        try {
            $result = $command.ExecuteNonQuery()
        }
        catch {
            Write-Error -Message "An error has occured. Error Details: $($_.Exception.Message)"
        }

        $ErrorActionPreference = 'Continue'

        if ($result -eq -1) {
            Write-Output $true
        }
        else {
            Write-Output $false
        }

    }

    END {

        $connection.Close()
        $connection.Dispose()

    }

}</pre>
The job returns a Boolean. True means it started successfully and false means it failed to start:
<pre class="lang:ps decode:true ">Start-MrSqlAgentJob -ServerInstance SQL011 -Name syspolicy_purge_history</pre>
<img class="alignnone size-full wp-image-14721" src="http://mikefrobbins.com/wp-content/uploads/2016/11/startsqljob2a.png" alt="startsqljob2a" width="859" height="72" />

The <em>Start-MrSqlAgentJob</em> function shown in the previous code example can be downloaded from <a href="https://github.com/mikefrobbins/SQL" target="_blank">my SQL repository on GitHub</a>.

<span style="text-decoration: underline;">Update</span>

Thanks to <a href="https://twitter.com/sqldbawithbeard" target="_blank">Rob Sewell</a> for pointing out that <em>Get-SqlAgentJob</em> returns a SMO object which has a start method:
<pre class="lang:ps decode:true">Get-SqlAgentJob -ServerInstance SQL011 -Name test | Get-Member -MemberType Method</pre>
<img class="alignnone size-full wp-image-14769" src="http://mikefrobbins.com/wp-content/uploads/2016/11/startsqljob4a.png" alt="startsqljob4a" width="859" height="634" />

That means it can be used to start a SQL agent job:
<pre class="lang:ps decode:true">(Get-SqlAgentJob -ServerInstance SQL011 -Name test).Start()
Get-SqlAgentJob -ServerInstance SQL011 -Name test</pre>
<img class="alignnone size-full wp-image-14770" src="http://mikefrobbins.com/wp-content/uploads/2016/11/startsqljob3a.png" alt="startsqljob3a" width="859" height="144" />

It does require that <a href="https://msdn.microsoft.com/en-us/library/mt238290.aspx" target="_blank">SQL Server Management Studio 2016</a> be installed on the machine it's being run from.

µ