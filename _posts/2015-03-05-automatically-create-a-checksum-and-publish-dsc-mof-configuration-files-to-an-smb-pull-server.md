---
ID: 11300
post_title: >
  Automatically create a checksum and
  publish DSC MOF configuration files to
  an SMB pull server
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/03/05/automatically-create-a-checksum-and-publish-dsc-mof-configuration-files-to-an-smb-pull-server/
published: true
post_date: 2015-03-05 07:30:39
---
You've configured one or more DSC (Desired State Configuration) SMB pull servers in your environment. You've also configured the target nodes appropriately. One problem that seems to be a constant problem in your environment when authoring and updating DSC configuration files (MOF files) is keeping track of what GUID belongs to which machine and it's also a common problem to forget to update the corresponding checksum when a configuration file is updated. Last week, you spent an entire day troubleshooting an issue where a machine wasn't pulling the updated configuration only to learn that the checksum hadn't been updated. Sound familiar?

To alleviate this problem, you've decided to write a PowerShell function that will be used to automatically determine the appropriate SMB share to copy the MOF configuration files to, determine the correct ConfigurationID (GUID), and generate the necessary checksum. The function to accomplish that task is shown in the following code example:
<pre class="lang:ps decode:true " title="Publish-MrMOFToSMB">#Requires -Version 4.0
function Publish-MrMOFToSMB {

&lt;#
.SYNOPSIS
    Publishes a DSC MOF configuration file to the pull server that's configured on a target node(s).
 
.DESCRIPTION
    Publish-MrMOFToSMB is an advanced PowerShell function that publishes one or more MOF configuration files
    to the an SMB DSC server by determining the ConfigurationID (GUID) that's configured on the target node along
    with the UNC path of the SMB pull server and creates the necessary checksum along with copying the MOF and
    checksum to the pull server.
 
.PARAMETER ConfigurationPath
    The folder path on the local computer that contains the mof configuration files.

.PARAMETER ComputerName
    The computer name of the target node that the DSC configuration is created for.
 
.EXAMPLE
     Publish-MrMOFToSMB -ConfigurationPath 'C:\MyMofFiles'

.EXAMPLE
     Publish-MrMOFToSMB -ConfigurationPath 'C:\MyMofFiles' -ComputerName 'Server01', 'Server02'

.EXAMPLE
     'Server01', 'Server02' | Publish-MrMOFToSMB -ConfigurationPath 'C:\MyMofFiles'

.EXAMPLE
     MyDscConfiguration -Param1 Value1 -Parm2 Value2 | Publish-MrMOFToSMB
 
.INPUTS
    String
 
.OUTPUTS
    None
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [Parameter(Mandatory,
                   ValueFromPipelineByPropertyName)]
        [ValidateScript({Test-Path -Path $_ -PathType Container})]
        [Alias('Directory')]
        [string]$ConfigurationPath,

        [Parameter(ValueFromPipeline,
                   ValueFromPipelineByPropertyName)]
        [Alias('BaseName')]
        [string[]]$ComputerName
    )

    BEGIN {
        if (-not($PSBoundParameters['ComputerName'])) {
            $ComputerName = (Get-ChildItem -Path $ConfigurationPath\*.mof).basename
        }
    }

    PROCESS {
        foreach ($Computer in $ComputerName) {

            try {
                Write-Verbose -Message "Retrieving LCM information from $Computer"
                $LCMConfig = Get-DscLocalConfigurationManager -CimSession $Computer -ErrorAction Stop
            }
            catch {
                Write-Error -Message "An error has occurred. Error details: $_.Exception.Message"
                continue
            }        
            
            $servermof = "$ConfigurationPath\$Computer.mof"

            if (-not(Get-ChildItem -Path $servermof -ErrorAction SilentlyContinue)) {
                Write-Error -Message "Unable to find MOF file for $Computer in location: $ConfigurationPath"
            } 
            elseif ($LCMConfig.RefreshMode -ne 'Pull') {
                Write-Error -Message "The LCM on $Computer is not configured for DSC pull mode."
            }
            elseif ($LCMConfig.DownloadManagerName -ne 'DscFileDownloadManager' -and $LCMConfig.ConfigurationDownloadManagers.ResourceId -notlike '`[ConfigurationRepositoryShare`]*') {
                Write-Error -Message "LCM on $Computer not configured to receive configuration from DSC SMB pull server"
            }
            elseif (-not($LCMConfig.ConfigurationID)) {
                Write-Error -Message "A ConfigurationID (GUID) has not been set in the LCM on $Computer"
            }
            else {
                if ($LCMConfig.ConfigurationDownloadManagers.SourcePath) {
                    $SMBPath = "$($LCMConfig.ConfigurationDownloadManagers.SourcePath)"
                }
                elseif ($LCMConfig.DownloadManagerCustomData.Value) {
                    $SMBPath = "$($LCMConfig.DownloadManagerCustomData.Value)"
                }

                Write-Verbose -Message "Creating DSCChecksum for $servermof"
                New-DSCCheckSum -ConfigurationPath $servermof -Force

                if (Test-Path -Path $SMBPath) {

                    $guidmof = Join-Path -Path $SMBPath -ChildPath "$($LCMConfig.ConfigurationID).mof"

                    try {
                        Write-Verbose -Message "Copying $servermof.checksum to $guidmof.checksum"
                        Copy-Item -Path "$servermof.checksum" -Destination "$guidmof.checksum" -ErrorAction Stop

                        Write-Verbose -Message "Copying $servermof to $guidmof"
                        Copy-Item -Path $servermof -Destination $guidmof -ErrorAction Stop
                    }
                    catch {
                        Write-Error -Message "An error has occurred. Error details: $_.Exception.Message"                    
                    }

                }
                else {
                    Write-Error -Message "Unable to connect to $SMBPath as specified in the LCM on $Computer for it's DSC pull server"
                }
            }
        }
    }
}</pre>
A very basic configuration will be used to test the function shown in the previous example.
<pre class="lang:ps decode:true ">configuration TelnetClient {
	param (
		[Parameter(Mandatory)]
		[string[]]$ComputerName
	)
	node $ComputerName {
        WindowsFeature TelnetClient {
            Name = 'Telnet-Client'
            Ensure = 'Present'
        }
    }
}</pre>
After the configuration is loaded into memory, it's called as it normally would be to generate the MOF configuration files and then it can simply be piped to the "<em>Publish-MrMOFToSMB</em>" function which will query the target node for the necessary information to automatically generate the required checksum and copy both the MOF configuration and checksum files to the SMB pull server, renaming the files to the GUID name on the destination end:
<pre class="lang:ps decode:true">TelnetClient -ComputerName 'Server01', 'Server02' | Publish-MrMOFToSMB -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/dscsmb1a.jpg"><img class="alignnone size-full wp-image-11302" src="http://mikefrobbins.com/wp-content/uploads/2015/03/dscsmb1a.jpg" alt="dscsmb1a" width="877" height="179" /></a>

If the MOF files have already been generated and you want to publish all of the ones in the .\TelnetClient folder to the appropriate SMB pull server, just specify that path via the <em>ConfigurationPath</em> parameter:
<pre class="lang:ps decode:true">Publish-MrMOFToSMB -ConfigurationPath .\TelnetClient -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/dscsmb2a.jpg"><img class="alignnone size-full wp-image-11305" src="http://mikefrobbins.com/wp-content/uploads/2015/03/dscsmb2a.jpg" alt="dscsmb2a" width="877" height="179" /></a>

If you only want to publish some of the existing MOF files in that folder, specify one or more computer names via the <em>ComputerName</em> parameter:
<pre class="lang:ps decode:true ">Publish-MrMOFToSMB -ComputerName server01 -ConfigurationPath .\TelnetClient -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/dscsmb3a.jpg"><img class="alignnone size-full wp-image-11306" src="http://mikefrobbins.com/wp-content/uploads/2015/03/dscsmb3a.jpg" alt="dscsmb3a" width="877" height="119" /></a>

For preexisting MOF files, the computer names can also be specified via parameter input which allows you to programmatically determine the computer names you want to publish the existing MOF files for by piping the results of some other cmdlet into this one:
<pre class="lang:ps decode:true">'server02' | Publish-MrMOFToSMB -ConfigurationPath .\TelnetClient -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/dscsmb4a.jpg"><img class="alignnone size-full wp-image-11307" src="http://mikefrobbins.com/wp-content/uploads/2015/03/dscsmb4a.jpg" alt="dscsmb4a" width="877" height="118" /></a>

Parameter validation is performed on the <em>ConfigurationPath</em> parameter which means if the path specified for the MOF files doesn't exist, the function will stop at that point and not attempt to continue any further:
<pre class="lang:ps decode:true">Publish-MrMOFToSMB -ComputerName server01 -ConfigurationPath .\DoesNotExist -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/dscsmb5b.jpg"><img class="alignnone size-full wp-image-11321" src="http://mikefrobbins.com/wp-content/uploads/2015/03/dscsmb5b.jpg" alt="dscsmb5b" width="877" height="167" /></a>

There are various other checks in place as well. Errors will be generated if the target node is offline, if the MOF file doesn't already exist, if the LCM on the target node is not configured for pull mode or for SMB, if the ConfigurationID hasn't been set to a GUID, and if the SMB share that's specified in the target node's LCM cannot be contacted:
<pre class="lang:ps decode:true">Publish-MrMOFToSMB -ComputerName server01, server05, sql01, dc01, server03, server04, dc02, server02 -ConfigurationPath .\TelnetClient -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/dscsmb6c.jpg"><img class="alignnone size-full wp-image-11325" src="http://mikefrobbins.com/wp-content/uploads/2015/03/dscsmb6c.jpg" alt="dscsmb6c" width="877" height="814" /></a>

Any of the ones that are specified which are valid will complete successfully as shown in the previous set of results where server01 and server02 (the first and last one) completed successfully even though all of the others failed for the specified reasons.

The <a href="https://gallery.technet.microsoft.com/scriptcenter/PowerShell-Function-to-3bc4b7f0" target="_blank"><em>Publish-MrMOFToSMB</em></a> PowerShell function shown in this blog article can be downloaded from the TechNet script repository.

µ