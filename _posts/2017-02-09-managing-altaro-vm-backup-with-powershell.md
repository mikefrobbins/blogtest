---
ID: 14999
post_title: >
  Managing Altaro VM Backup with
  PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/02/09/managing-altaro-vm-backup-with-powershell/
published: true
post_date: 2017-02-09 07:30:40
---
Recently, I decided to try to determine if there was a way to manage Altaro VM Backup with PowerShell. I figured there must be some database, files, or something that I could at least query with PowerShell.

What I found is Altaro has a RESTful API and they have numerous PowerShell scripts for working with it. In Altaro version 7, there are 33 PowerShell scripts located in the "C:\Program Files\Altaro\Altaro Backup\Cmdlets" folder if you took the defaults during the installation, otherwise they're wherever you installed Altaro. Altaro has <a href="http://www.altaro.com/hyper-v/introducing-the-altaro-vm-backup-api/" target="_blank">a blog article on their website</a> announcing the API in March of 2016.

After taking a look at Altaro's PowerShell scripts, I decided not to use them. I started writing my own PowerShell module with functions for working with their API.

Although their API has a limitation that it only works locally on the actual Altaro VM Backup server, that doesn't mean that you can't use it with PowerShell remoting. Working remotely is always better from a manageability standpoint since using remote desktop to connect to the server only to run PowerShell on it locally defeats the whole purpose of using PowerShell.

I'll start out by storing my credentials in a variable named Cred:
<pre class="lang:ps decode:true ">$Cred = Get-Credential</pre>
Use one-to-one remoting to access my Altaro VM Backup server:
<pre class="lang:ps decode:true">Enter-PSSession -ComputerName mr101 -Credential $Cred</pre>
The Altaro API service is disabled by default. Set it to start automatically and then start the service:
<pre class="lang:ps decode:true ">Get-Service -DisplayName 'Altaro VM Backup API Service' |
Set-Service -StartupType Automatic -PassThru |
Start-Service -PassThru</pre>
<img class="alignnone size-full wp-image-15026" src="http://mikefrobbins.com/wp-content/uploads/2017/02/altaro-api9b.png" alt="" width="859" height="153" />

The <em>Start-MrAltaroSession</em> function shown in the following code example is used to start a session with the API on an Altaro VM Backup server:
<pre class="lang:ps decode:true">#Requires -Version 3.0
function Start-MrAltaroSession {

&lt;#
.SYNOPSIS
    Starts a new session with the RESTful API of an Altaro VM Backup server.
 
.DESCRIPTION
    Start-MrAltaroSession is an advanced function that starts a new session with the RESTful API of an
    Altaro VM Backup server. In it current interation, the Altaro RESTful API can only be used locally
    on the Altaro VM Backup server.
 
.PARAMETER ComputerName
    Name of the Altaro VM Backup server. The default is localhost. Currently, the Altaro API only works
    with localhost. $env:COMPUTERNAME or the actual computer name does not work.

.PARAMETER Port
    Port number that the Altaro VM Backup API is listening on. The default is 35113.

.PARAMETER Credential
    The credentials for connecting to the Altaro VM Backup API.
 
.EXAMPLE
     Start-MrAltaroSession -Credential (Get-Credential)

.EXAMPLE
     Start-MrAltaroSession -ComputerName localhost -Credential (Get-Credential)

.EXAMPLE
     Start-MrAltaroSession -ComputerName localhost -Port 35113 -Credential (Get-Credential)

.INPUTS
    None
 
.OUTPUTS
    PSCustomObject
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [ValidateNotNullOrEmpty()]
        [Alias('ServerName')]
        [string]$ComputerName = 'localhost',
        
        [ValidateNotNullOrEmpty()]
        [int]$Port = 35113,
        
        [Parameter(Mandatory)]
        [System.Management.Automation.Credential()]$Credential
    )
    
    $Uri = "http://$ComputerName`:$Port/api/sessions/start"

    $Body = @{
        ServerAddress = $ComputerName 
        ServerPort = '35107' 
        Username = $Credential.UserName -replace '^.*\\'
        Password = $Credential.GetNetworkCredential().Password
        Domain = $Credential.UserName -replace '\\.*$'
    } | ConvertTo-Json
    
    try {
        $Results =  Invoke-RestMethod -Uri $uri -Method Post -ContentType 'application/json' -Body $Body
    }
    catch {
        Write-Error -Message "Unable to connect to Altaro API at: $Uri"
    }

    if ($Results.Success -eq $true) {    
        [PSCustomObject]@{
            SessionId = [guid]$Results.Data
        }
    }
    elseif ($Results.Success -eq $false) {
        Write-Error -Message "$($Results.ErrorMessage). Error Code: $($Results.ErrorCode). $($Results.ErrorAdditionalDetails)"
    }
}</pre>
From this point forward, I can use the Using variable scope modifier with the credentials I stored in the local variable named Cred on the remote server.

Use the <em>Start-MrAltaroSession</em> function to start a new session with the API:
<pre class="lang:ps decode:true">Start-MrAltaroSession -Credential $Using:Cred</pre>
<img class="alignnone size-full wp-image-15015" src="http://mikefrobbins.com/wp-content/uploads/2017/02/altaro-api1b.png" alt="" width="859" height="132" />

Errors are returned as an actual error:
<pre class="lang:ps decode:true">Start-MrAltaroSession -Credential $Using:Cred</pre>
<img class="alignnone size-full wp-image-15016" src="http://mikefrobbins.com/wp-content/uploads/2017/02/altaro-api2b.png" alt="" width="859" height="130" />

The <em>Get-MrAltaroOperationStatus</em> function is used to retrieve the status of operations that are currently running:
<pre class="lang:ps decode:true">#Requires -Version 3.0
function Get-MrAltaroOperationStatus {

&lt;#
.SYNOPSIS
    Retrieves the status and progress for Altaro VM Backup jobs which are currently running.
 
.DESCRIPTION
    Get-MrAltaroOperationStatus is an advanced function that retrieves the status and progress for Altaro
    VM Backup jobs (backups, offsite copies, restores, seed to disks) which are currently running.
 
.PARAMETER ComputerName
    Name of the Altaro VM Backup server. The default is localhost. Currently, the Altaro API only works
    with localhost. $env:COMPUTERNAME or the actual computer name does not work.

.PARAMETER Port
    Port number that the Altaro VM Backup API is listening on. The default is 35113.

.PARAMETER SessionId
    The Id in the form of a GUID for the sesison created by Start-MrAltaroSession.

.PARAMETER OperationId
    The Id in the form of a GUID for the specific operation to return results for.
 
.EXAMPLE
     Get-MrAltaroOperationStatus -SessionId b3019809-cfbe-4ac6-93df-a24c91b5b28e

.EXAMPLE
     Get-MrAltaroOperationStatus -SessionId b3019809-cfbe-4ac6-93df-a24c91b5b28e -OperationId 1351d928-0391-4a45-8d5a-ae4b806bea66

.EXAMPLE
     Get-MrAltaroOperationStatus -ComputerName localhost -Port 35113 -SessionId b3019809-cfbe-4ac6-93df-a24c91b5b28e

.INPUTS
    Guid
 
.OUTPUTS
    PSCustomObject
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [ValidateNotNullOrEmpty()]
        [Alias('ServerName')]
        [string]$ComputerName = 'localhost',
        
        [ValidateNotNullOrEmpty()]
        [int]$Port = 35113,
        
        [Parameter(Mandatory,
                   ValueFromPipeline,
                   ValueFromPipelineByPropertyName)]
        [guid]$SessionId,

        [guid]$OperationId
    )

    PROCESS {    
        $uri = "http://$ComputerName`:$Port/api/activity/operation-status/$SessionId"
        
        Invoke-RestMethod -Uri $uri -Method Get -ContentType 'application/json' |
        Select-Object -ExpandProperty Statuses
    }

}</pre>
These include backups, offsite copies, restores, and seed to disks operations:
<pre class="lang:ps decode:true">Get-MrAltaroOperationStatus -SessionId fe0a7c65-1fab-474c-bf2f-4a2a2f8f2b89</pre>
<img class="alignnone size-full wp-image-15017" src="http://mikefrobbins.com/wp-content/uploads/2017/02/altaro-api3b.png" alt="" width="859" height="214" />

You can also pipe <em>Start-MrAltaroSession</em> directly to <em>Get-MrAltaroOperationStatus</em>:
<pre class="lang:ps decode:true ">Start-MrAltaroSession -Credential $Using:Cred | Get-MrAltaroOperationStatus</pre>
<img class="alignnone size-full wp-image-15018" src="http://mikefrobbins.com/wp-content/uploads/2017/02/altaro-api4b.png" alt="" width="859" height="212" />

The <em>Stop-MrAltaroSession</em> function gracefully closes out of one or more sessions that were previously opened:
<pre class="lang:ps decode:true ">#Requires -Version 3.0
function Stop-MrAltaroSession {

&lt;#
.SYNOPSIS
    Stops one or more sessions with the RESTful API of an Altaro VM Backup server.
 
.DESCRIPTION
    Stop-MrAltaroSession is an advanced function that stops one or more sessions with the RESTful API of
    an Altaro VM Backup server. In it current interation, the Altaro RESTful API can only be used locally
    on the Altaro VM Backup server.
 
.PARAMETER ComputerName
    Name of the Altaro VM Backup server. The default is localhost. Currently, the Altaro API only works
    with localhost. $env:COMPUTERNAME or the actual computer name does not work.

.PARAMETER Port
    Port number that the Altaro VM Backup API is listening on. The default is 35113.

.PARAMETER SessionId
    The Id in the form of a GUID for the sesison created by Start-MrAltaroSession.
 
.EXAMPLE
     Stop-MrAltaroSession

.EXAMPLE
     Stop-MrAltaroSession -SessionId 6d2d22ef-06df-4bb0-976b-bb6a8b3f2683

.EXAMPLE
     Stop-MrAltaroSession -ComputerName localhost -Port 35113 -SessionId 66c09e7d-e10c-4608-b9cd-d35579784e70

.INPUTS
    None
 
.OUTPUTS
    PSCustomObject
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [ValidateNotNullOrEmpty()]
        [Alias('ServerName')]
        [string]$ComputerName = 'localhost',
        
        [ValidateNotNullOrEmpty()]
        [int]$Port = 35113,

        [Parameter(ValueFromPipeline,
                   ValueFromPipelineByPropertyName)]
        [guid]$SessionId
    )

    PROCESS {
        $uri = "http://$ComputerName`:$Port/api/sessions/end"

        if ($PSBoundParameters.SessionId){
            $uri = "$uri/$SessionId"
        }
    
        try {
            $Results =  Invoke-RestMethod -Uri $uri -Method Post -ContentType 'application/json'
        }
        catch {
            Write-Error -Message "Unable to connect to Altaro API at: $Uri"
        }

        if ($Results.Success -eq $true -and $Results.ClosedSessions.SessionToken -ne $null) {    
            foreach ($Result in $Results.ClosedSessions) {
                [PSCustomObject]@{
                    SessionId = [guid]$Result.SessionToken
                }
            }
        }
        elseif ($Results.Success -eq $true -and $Results.ClosedSessions.SessionToken -eq $null){
            Write-Warning -Message 'There are no open sessions.'
        }
        elseif ($Results.Success -eq $false) {
            Write-Error -Message "$($Results.ErrorMessage). Error Code: $($Results.ErrorCode). $($Results.ErrorAdditionalDetails)"
        }
    }

}</pre>
It can be used to stop a single session. If multiple sessions existed, the <em>SessionId</em> parameter could be specified to stop a single session.
<pre class="lang:ps decode:true">Stop-MrAltaroSession</pre>
<img class="alignnone size-full wp-image-15019" src="http://mikefrobbins.com/wp-content/uploads/2017/02/altaro-api5b.png" alt="" width="859" height="131" />

By default, if multiple sessions exist, it stops all of them:
<pre class="lang:ps decode:true">Stop-MrAltaroSession</pre>
<img class="alignnone size-full wp-image-15020" src="http://mikefrobbins.com/wp-content/uploads/2017/02/altaro-api6b.png" alt="" width="859" height="155" />

A warning is returned if no sessions exist:
<pre class="lang:ps decode:true ">Stop-MrAltaroSession</pre>
<img class="alignnone size-full wp-image-15021" src="http://mikefrobbins.com/wp-content/uploads/2017/02/altaro-api7b.png" alt="" width="859" height="68" />

The functions shown in this blog article are part of my MrAltaro PowerShell script module which can be downloaded from <a href="https://github.com/mikefrobbins/Altaro" target="_blank">my Altaro repository on GitHub</a>.

For more information about the Altaro RESTful API, <a href="http://www.altaro.com/support/api/doc/Content/Home.htm" target="_blank">see their documentation page</a>.

µ