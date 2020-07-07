---
ID: 12628
post_title: 'Solving DSC Problems on Windows 10 &#038; Writing PowerShell Code that writes PowerShell Code for you'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/11/05/solving-dsc-problems-on-windows-10-writing-powershell-code-that-writes-powershell-code-for-you/
published: true
post_date: 2015-11-05 07:30:11
---
I recently ran into a problem with DSC on Windows 10 when trying to create MOF files with DSC configurations that work on other operating systems. An error is generated when the friendly name for a DSC resource contains a dash and that friendly name is specified as a dependency for another resource. I know that only certain characters are allowed in the name that's specified for DependsOn and I've run into similar problems with things such as IP addresses due to the dot or period, but the dash works in other operating systems at least with the production preview of PowerShell version 5, but not with the version of PowerShell version 5 that ships with Windows 10:
<pre class="lang:ps decode:true">configuration ConfigureSQLServer {

    Import-DscResource -ModuleName PSDesiredStateConfiguration, xSqlPs

    node Server01 {
        WindowsFeature Net-Framework-Core {
            Name = 'Net-Framework-Core'
            Ensure = 'Present'
        }

        xSqlServerInstall InstallSQLEngine {
            InstanceName = 'MSSQLSERVER'
            SourcePath = 'D:'
            Features= 'SQLEngine' 
            DependsOn ='[WindowsFeature]Net-Framework-Core'
        }
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/win10-dsc1a.jpg"><img class="alignnone size-full wp-image-12655" src="http://mikefrobbins.com/wp-content/uploads/2015/10/win10-dsc1a.jpg" alt="win10-dsc1a" width="859" height="262" /></a>

<span style="color: #ff0000;"><em>Test-DependsOn : The format of the resource reference '[WindowsFeature]Net-Framework-Core' in the Requires list for</em></span>
<span style="color: #ff0000;"><em>resource '[xSqlServerInstall]InstallSQLEngine' is not valid. A required resource name should be in the format</em></span>
<span style="color: #ff0000;"><em>'[&lt;typename&gt;]&lt;name&gt;', with no spaces.</em></span>
<span style="color: #ff0000;"><em>At line:117 char:13</em></span>
<span style="color: #ff0000;"><em>+ Test-DependsOn</em></span>
<span style="color: #ff0000;"><em>+ ~~~~~~~~~~~~~~</em></span>
<span style="color: #ff0000;"><em> + CategoryInfo : InvalidOperation: (:) [Write-Error], ParentContainsErrorRecordException</em></span>
<span style="color: #ff0000;"><em> + FullyQualifiedErrorId : GetBadlyFormedRequiredResourceId,Test-DependsOn</em></span>

<span style="color: #ff0000;"><em>Errors occurred while processing configuration 'ConfigureSQLServer'.</em></span>
<span style="color: #ff0000;"><em>At</em></span>
<span style="color: #ff0000;"><em>C:\Windows\system32\WindowsPowerShell\v1.0\Modules\PSDesiredStateConfiguration\PSDesiredStateConfiguration.psm1:3588</em></span>
<span style="color: #ff0000;"><em>char:5</em></span>
<span style="color: #ff0000;"><em>+ throw $ErrorRecord</em></span>
<span style="color: #ff0000;"><em>+ ~~~~~~~~~~~~~~~~~~</em></span>
<span style="color: #ff0000;"><em> + CategoryInfo : InvalidOperation: (ConfigureSQLServer:String) [], InvalidOperationException</em></span>
<span style="color: #ff0000;"><em> + FullyQualifiedErrorId : FailToProcessConfiguration</em></span>

Modifying the configuration to fix the problem for this example isn't difficult since only one feature is specified:
<pre class="lang:ps decode:true">configuration ConfigureSQLServer {

    Import-DscResource -ModuleName PSDesiredStateConfiguration, xSqlPs

    node Server01 {
        WindowsFeature NetFrameworkCore {
            Name = 'Net-Framework-Core'
            Ensure = 'Present'
        }

        xSqlServerInstall InstallSQLEngine {
            InstanceName = 'MSSQLSERVER'
            SourcePath = 'D:'
            Features= 'SQLEngine' 
            DependsOn ='[WindowsFeature]NetFrameworkCore'
        }
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/win10-dsc2a.jpg"><img class="alignnone size-full wp-image-12657" src="http://mikefrobbins.com/wp-content/uploads/2015/10/win10-dsc2a.jpg" alt="win10-dsc2a" width="859" height="179" /></a>

This becomes a bigger problem when lots of Windows Features are added to the configuration. Maybe I'm building an additional SQL Server and I want to install all of the features that are currently installed on an existing SQL Server.

Note: This would only be a problem if they had dependencies set in the configuration, but maybe I want to be proactive and eliminate the dashes to prevent problems moving forward and not each time that I add a dependency.
<pre class="lang:ps decode:true">Invoke-Command -ComputerName SQL04 {
    Get-WindowsFeature | Where-Object Installed -eq $True
} | Select-Object -ExpandProperty Name</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/win10-dsc3a.jpg"><img class="alignnone size-full wp-image-12659" src="http://mikefrobbins.com/wp-content/uploads/2015/10/win10-dsc3a.jpg" alt="win10-dsc3a" width="859" height="250" /></a>

That existing SQL Server currently has 14 roles that are enabled. That would require a lot of manual tweaking to remove all of the dashes as demonstrated in the previous example. What I can do though is to use PowerShell to generate the code for that portion of the configuration and paste it in instead of having to write it all manually:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName SQL04 {
    Get-WindowsFeature | Where-Object Installed -eq $True
} | Select-Object -ExpandProperty Name |
ForEach-Object {
"        WindowsFeature $($_ -replace '-','') {"
"            Name = '$_'"
"            Ensure = 'Present'"
"        }"
} | clip</pre>
Although this configuration is valid, it contains a lot of somewhat static code to maintain. There has to be a better way, but if you're going to do it this way you might as well at least have PowerShell write some of the code for you.
<pre class="lang:ps decode:true ">configuration ConfigureSQLServer {

    Import-DscResource -ModuleName PSDesiredStateConfiguration, xSqlPs

    node Server01 {
        WindowsFeature FileAndStorageServices {
            Name = 'FileAndStorage-Services'
            Ensure = 'Present'
        }
        WindowsFeature StorageServices {
            Name = 'Storage-Services'
            Ensure = 'Present'
        }
        WindowsFeature NETFrameworkFeatures {
            Name = 'NET-Framework-Features'
            Ensure = 'Present'
        }
        WindowsFeature NETFrameworkCore {
            Name = 'NET-Framework-Core'
            Ensure = 'Present'
        }
        WindowsFeature NETFramework45Features {
            Name = 'NET-Framework-45-Features'
            Ensure = 'Present'
        }
        WindowsFeature NETFramework45Core {
            Name = 'NET-Framework-45-Core'
            Ensure = 'Present'
        }
        WindowsFeature NETWCFServices45 {
            Name = 'NET-WCF-Services45'
            Ensure = 'Present'
        }
        WindowsFeature NETWCFTCPPortSharing45 {
            Name = 'NET-WCF-TCP-PortSharing45'
            Ensure = 'Present'
        }
        WindowsFeature MultipathIO {
            Name = 'Multipath-IO'
            Ensure = 'Present'
        }
        WindowsFeature FSSMB1 {
            Name = 'FS-SMB1'
            Ensure = 'Present'
        }
        WindowsFeature UserInterfacesInfra {
            Name = 'User-Interfaces-Infra'
            Ensure = 'Present'
        }
        WindowsFeature PowerShellRoot {
            Name = 'PowerShellRoot'
            Ensure = 'Present'
        }
        WindowsFeature PowerShell {
            Name = 'PowerShell'
            Ensure = 'Present'
        }
        WindowsFeature WoW64Support {
            Name = 'WoW64-Support'
            Ensure = 'Present'
        }
        xSqlServerInstall InstallSQLEngine {
            InstanceName = 'MSSQLSERVER'
            SourcePath = 'D:'
            Features= 'SQLEngine' 
            DependsOn ='[WindowsFeature]NetFrameworkCore'
        }
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/win10-dsc4a.jpg"><img class="alignnone size-full wp-image-12662" src="http://mikefrobbins.com/wp-content/uploads/2015/10/win10-dsc4a.jpg" alt="win10-dsc4a" width="859" height="178" /></a>

Let's try separating the environmental configuration from the structural configuration to see if that reduces the amount of what seems to be redundant code. The structural configuration is very concise:
<pre class="lang:ps decode:true">configuration ConfigureSQLServer {

    Import-DscResource -ModuleName PSDesiredStateConfiguration, xSqlPs

    node $AllNodes.NodeName {

        $Node.WindowsFeature.ForEach({
            WindowsFeature $_.ID  {
                Name = $_.Name
                Ensure = 'Present'
            }
        })
        
        xSqlServerInstall InstallSQLEngine {
            InstanceName = $Node.InstanceName
            SourcePath = $Node.SourcePath
            Features= $Node.Features
            DependsOn = '[WindowsFeature]NetFrameworkCore'
        }
    }
}</pre>
For the environmental configuration, we'll need to create a hash table for each of the Windows Features that we want to install. We'll use PowerShell again to write all of those hash tables for us:
<pre class="lang:ps decode:true ">Invoke-Command -ComputerName SQL04 {
    Get-WindowsFeature | Where-Object Installed -eq $True
} | Select-Object -ExpandProperty Name |
ForEach-Object {
"                @{"
"                    ID = '$($_ -replace '-','')'"
"                    Name = '$_'"
"                }"
} | clip</pre>
Then simply paste that portion of the code instead of having to manually type it all in manually:
<pre class="lang:ps decode:true">$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'SQL04'
            InstanceName = 'MSSQLSERVER'
            SourcePath =  'D:'
            Features = 'SQLEngine'
            WindowsFeature = @(
                @{
                    ID = 'FileAndStorageServices'
                    Name = 'FileAndStorage-Services'
                }
                @{
                    ID = 'StorageServices'
                    Name = 'Storage-Services'
                }
                @{
                    ID = 'NETFrameworkFeatures'
                    Name = 'NET-Framework-Features'
                }
                @{
                    ID = 'NETFrameworkCore'
                    Name = 'NET-Framework-Core'
                }
                @{
                    ID = 'NETFramework45Features'
                    Name = 'NET-Framework-45-Features'
                }
                @{
                    ID = 'NETFramework45Core'
                    Name = 'NET-Framework-45-Core'
                }
                @{
                    ID = 'NETWCFServices45'
                    Name = 'NET-WCF-Services45'
                }
                @{
                    ID = 'NETWCFTCPPortSharing45'
                    Name = 'NET-WCF-TCP-PortSharing45'
                }
                @{
                    ID = 'MultipathIO'
                    Name = 'Multipath-IO'
                }
                @{
                    ID = 'FSSMB1'
                    Name = 'FS-SMB1'
                }
                @{
                    ID = 'UserInterfacesInfra'
                    Name = 'User-Interfaces-Infra'
                }
                @{
                    ID = 'PowerShellRoot'
                    Name = 'PowerShellRoot'
                }
                @{
                    ID = 'PowerShell'
                    Name = 'PowerShell'
                }
                @{
                    ID = 'WoW64Support'
                    Name = 'WoW64-Support'
                }
            )
        }
    )
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/win10-dsc5a.jpg"><img class="alignnone size-full wp-image-12663" src="http://mikefrobbins.com/wp-content/uploads/2015/10/win10-dsc5a.jpg" alt="win10-dsc5a" width="859" height="179" /></a>

That's still an enormous amount of somewhat redundant code to have to maintain. There must be a better way than having so many lines of code for each feature so let's try going another route. We'll add a small amount of complexity to our configuration so the dash (-) can be stripped out on the fly using the replace operator. The replace method could also be used. I generally prefer to use the replace operator instead of the replace method since the replace method is case sensitive and the replace operator is not but it doesn't matter in this scenario.
<pre class="lang:ps decode:true">configuration ConfigureSQLServer {

    Import-DscResource -ModuleName PSDesiredStateConfiguration, xSqlPs

    node $AllNodes.NodeName {

        $Node.WindowsFeature.ForEach({
            WindowsFeature ($_ -replace '-','') {
                Name = $_
                Ensure = 'Present'
            }
        })
        xSqlServerInstall InstallSQLEngine {
            InstanceName = $Node.InstanceName
            SourcePath = $Node.SourcePath
            Features= $Node.Features
            DependsOn = '[WindowsFeature]NetFrameworkCore'
        }
    }
}</pre>
Let's use PowerShell to generate a comma separated list of Windows Features based on the ones that are enabled on SQL04. I've seen people write an enormous amount of code to accomplish something like this which is simple to do:
<pre class="lang:ps decode:true">(Invoke-Command -ComputerName SQL04 {
    Get-WindowsFeature | Where-Object Installed -eq $True
}).Name -join "','" |
clip</pre>
I'll use that comma separated list to create a new more simplistic environmental configuration:
<pre class="lang:ps decode:true">$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'SQL04'
            InstanceName = 'MSSQLSERVER'
            SourcePath =  'D:'
            Features = 'SQLEngine'
            WindowsFeature = 'FileAndStorage-Services','Storage-Services','NET-Framework-Features','NET-Framework-Core',
                             'NET-Framework-45-Features','NET-Framework-45-Core','NET-WCF-Services45','NET-WCF-TCP-PortSharing45',
                             'Multipath-IO','FS-SMB1','User-Interfaces-Infra','PowerShellRoot','PowerShell','WoW64-Support'
        }
    )
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/win10-dsc6a.jpg"><img class="alignnone size-full wp-image-12664" src="http://mikefrobbins.com/wp-content/uploads/2015/10/win10-dsc6a.jpg" alt="win10-dsc6a" width="859" height="180" /></a>

You could also place code to generate the list of features directly inside the environmental configuration as long as you're storing the hash table in a variable:
<pre class="lang:ps decode:true">$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'SQL04'
            InstanceName = 'MSSQLSERVER'
            SourcePath =  'D:'
            Features = 'SQLEngine'
            WindowsFeature = (Invoke-Command -ComputerName SQL04 {
                                Get-WindowsFeature | Where-Object Installed -eq $True
                             } | Select-Object -ExpandProperty Name)
        }
    )
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/win10-dsc7a.jpg"><img class="alignnone size-full wp-image-12665" src="http://mikefrobbins.com/wp-content/uploads/2015/10/win10-dsc7a.jpg" alt="win10-dsc7a" width="859" height="178" /></a>

If you're going to store the hash table for the environmental configuration in a PSD1 file, you can't use PowerShell code such as what's shown in the previous example. The hash table has to be static when it's saved in a PSD1 file.
<pre class="lang:ps decode:true ">New-Item -Path .\configdata.psd1 -ItemType File -Force -Value "
@{
    AllNodes = @(
        @{
            NodeName = 'SQL04'
            InstanceName = 'MSSQLSERVER'
            SourcePath =  'D:'
            Features = 'SQLEngine'
            WindowsFeature = 'FileAndStorage-Services','Storage-Services','NET-Framework-Features','NET-Framework-Core',
                             'NET-Framework-45-Features','NET-Framework-45-Core','NET-WCF-Services45','NET-WCF-TCP-PortSharing45',
                             'Multipath-IO','FS-SMB1','User-Interfaces-Infra','PowerShellRoot','PowerShell','WoW64-Support'
        }
    )
}"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/win10-dsc8a.jpg"><img class="alignnone size-full wp-image-12667" src="http://mikefrobbins.com/wp-content/uploads/2015/10/win10-dsc8a.jpg" alt="win10-dsc8a" width="859" height="372" /></a>

Create the MOF file using the configuration data stored in a PSD1 file:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/win10-dsc9a.jpg"><img class="alignnone size-full wp-image-12668" src="http://mikefrobbins.com/wp-content/uploads/2015/10/win10-dsc9a.jpg" alt="win10-dsc9a" width="859" height="179" /></a>

Mark Twain once said <em>“I didn't have time to write a short letter, so I wrote a long one instead.”</em> That's similar to how writing DSC configurations work.

A quick and dirty DSC configuration is often long and complicated because making them short and simple requires a little more time and effort. That additional time and effort will pay off big time in the long run though by saving your sanity when you have to revisit or troubleshoot it in the future.

µ