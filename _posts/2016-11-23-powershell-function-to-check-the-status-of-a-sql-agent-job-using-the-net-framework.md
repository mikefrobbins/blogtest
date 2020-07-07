---
ID: 14750
post_title: >
  PowerShell Function to Check the Status
  of a SQL Agent Job using the .NET
  Framework
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/11/23/powershell-function-to-check-the-status-of-a-sql-agent-job-using-the-net-framework/
published: true
post_date: 2016-11-23 07:30:39
---
<a href="http://mikefrobbins.com/2016/11/22/start-a-sql-agent-job-with-the-net-framework-from-powershell/" target="_blank">My previous blog article</a> demonstrated how to start a SQL agent job using the .NET Framework from PowerShell to eliminate the dependency of needing the SQL Server PowerShell module or snap-in on the machine where the command is being run from.

There's not much use of blindly starting a SQL agent job without being able to check the status of it so I decided to write another function to accomplish that task. If the machine where the code is being run from has the SQL Server PowerShell module that installs as part of <a href="https://msdn.microsoft.com/en-us/library/mt238290.aspx" target="_blank">SQL Server Management Studio 2016</a>, then you could simply use existing cmdlets without having to use this custom function.
<pre class="lang:ps decode:true " title="Get-MrSqlAgentJobStatus">#Requires -Version 3.0
function Get-MrSqlAgentJobStatus {

&lt;#
.SYNOPSIS
    Retrieves the status of one or more SQL Agent Jobs from the specified target instance of SQL Server.
 
.DESCRIPTION
    Get-MrSqlAgentJobStatus is a PowerShell function that is designed to retrieve the status of one or more SQL
    Server Agent jobs from the specified target instance of SQL Server without requiring the SQL Server PowerShell
    module or snap-in to be installed.
 
.PARAMETER ServerInstance
    The name of an instance of SQL Server where the SQL Agent is running. For default instances, only
    specify the computer name: MyComputer. For named instances, use the format ComputerName\InstanceName.
 
.PARAMETER Name
    Specifies the name of one or more Job objects that this cmdlet gets. The names may or may not be
    case-sensitive, depending on the collation of the SQL Server where the SQL Agent is running.

.PARAMETER Credential
    SQL Authentication userid and password in the form of a credential object.
 
.EXAMPLE
     Get-MrSqlAgentJobStatus -ServerInstance SQLServer01 -Name syspolicy_purge_history, test

.EXAMPLE
     Get-MrSqlAgentJobStatus -ServerInstance SQLServer01 -Name syspolicy_purge_history -Credential (Get-Credential)

.EXAMPLE
     'syspolicy_purge_history', 'test' | Get-MrSqlAgentJobStatus -ServerInstance SQLServer01

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
        [Parameter(Mandatory)]
        [string]$ServerInstance,

        [Parameter(Mandatory,
                   ValueFromPipeLine)]
        [string[]]$Name,

        [System.Management.Automation.Credential()]$Credential = [System.Management.Automation.PSCredential]::Empty
    )

    BEGIN {
    
        $Params = @{
            ServerInstance = $ServerInstance
            Database = 'msdb'
        }

        if ($PSBoundParameters.Credential) {
            $Params.Credential = $Credential
        }

    }

    PROCESS {
        foreach ($n in $Name) {
        
            $Params.Query = "EXEC dbo.sp_help_job @JOB_NAME = '$n', @job_aspect= 'JOB'"
            $Results = Invoke-MrSqlDataReader @Params |
                       Select-Object -Property originating_server, name, last_run_outcome, current_execution_status

            [pscustomobject]@{
                ComputerName = $Results.originating_server
                Name = $Results.name
                Outcome = switch ($Results.current_execution_status) {
                              1 {'Executing';break}
                              2 {'Waiting for thread';break}
                              3 {'Between retries';break}
                              4 {'Idle';break}
                              5 {'Suspended';break}
                              7 {'Performing completion actions';break}        
                              default {'An unknown error has occurred'}
                          }
                Status = switch ($Results.last_run_outcome) {
                             0 {'Failed';break}
                             1 {'Succeeded';break}
                             3 {'Canceled';break}
                             5 {'Unknown';break}     
                             default {'An unknown error has occurred'}
                         }
            }
        
        }

    }

}</pre>
Specify the SQL instance name and job name to return the status. There's also a credential parameter to specify SQL authentication if needed.
<pre class="lang:ps decode:true">Get-MrSqlAgentJobStatus -ServerInstance SQL011 -Name syspolicy_purge_history</pre>
<img class="alignnone size-full wp-image-14755" src="http://mikefrobbins.com/wp-content/uploads/2016/11/agentjobstatus1a.png" alt="agentjobstatus1a" width="859" height="131" />

Multiple jobs can also be retrieved via parameter or pipeline input:
<pre class="lang:ps decode:true ">Get-MrSqlAgentJobStatus -ServerInstance SQL011 -Name syspolicy_purge_history, test
syspolicy_purge_history', 'test' | Get-MrSqlAgentJobStatus -ServerInstance SQL011</pre>
<img class="alignnone size-full wp-image-14756" src="http://mikefrobbins.com/wp-content/uploads/2016/11/agentjobstatus2a.png" alt="agentjobstatus2a" width="859" height="240" />

The <em>Get-MrSqlAgentJobStatus</em> function shown in this blog article can be downloaded from <a href="https://github.com/mikefrobbins/SQL" target="_blank">my SQL repository on GitHub</a>. This function depends on my <em>Invoke-MrSqlDataReader</em> function which is also part of my MrSQL PowerShell module which can be found in the same repo.

µ