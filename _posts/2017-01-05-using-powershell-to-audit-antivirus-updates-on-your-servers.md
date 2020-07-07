---
ID: 14880
post_title: >
  Using PowerShell to Audit Antivirus
  Updates on your Servers
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/01/05/using-powershell-to-audit-antivirus-updates-on-your-servers/
published: true
post_date: 2017-01-05 07:30:37
---
How often do you check to make sure that things like antivirus has received the latest definition files on all of your servers? There's probably some centralized GUI interface somewhere that you could log into and check. The antivirus product itself may even have some sort of notification system that sends alerts if the updates fail. Both of those options provide data in a format that can't be worked with and what happens if something falls through the cracks? Are you willing to bet your job and possible the reputation of your company that some junior level engineer is monitoring those systems?

While each antivirus product is different, it's fairly simple to determine if the information for the antivirus product is stored somewhere such as in the registry where you can access it remotely with PowerShell. The following example was my first attempt at querying this information for ESET File Security:
<pre class="lang:ps decode:true">function Get-MrEsetUpdateVersion {
    [CmdletBinding()]
    param (
        [string[]]$ComputerName,
        [System.Management.Automation.Credential()]$Credential = [System.Management.Automation.PSCredential]::Empty
    )

    $Params = @{}
    if ($PSBoundParameters.Credential){
        $Params.Credential = $Credential
    }
    
    foreach ($Computer in $ComputerName){
        $Results = Invoke-Command -ComputerName $Computer {
            Get-ItemProperty -Path 'HKLM:\SOFTWARE\ESET\ESET Security\CurrentVersion\Info' -ErrorAction SilentlyContinue
        } @Params

        [pscustomobject]@{
            ComputerName = $Computer
            ProductName = $Results.ProductName
            ScannerVersion = $Results.ScannerVersionId
            LastUpdate = $Results.ScannerVersion -replace '^.*\(|\)'
        }

    }

}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/01/eset-version1b.png"><img class="alignnone size-full wp-image-14898" src="http://mikefrobbins.com/wp-content/uploads/2017/01/eset-version1b.png" alt="" width="859" height="152" /></a>

Not bad, but the problem is that it queries each server individually (one at a time) which takes more time than necessary and the last update property is returned in a format that isn't in a date/time datatype.

The problem with trying to query all of the servers in parallel with <em>Invoke-Command</em> is keeping up with the computer name for the ones that don't have that particular registry key which means they don't have ESET File Security installed.
<pre class="lang:ps decode:true">function Get-MrEsetUpdateVersion {
    [CmdletBinding()]
    param (
        [ValidateNotNullOrEmpty()]
        [string[]]$ComputerName = $env:COMPUTERNAME,
        
        [System.Management.Automation.Credential()]$Credential = [System.Management.Automation.PSCredential]::Empty
    )

    $Params = @{}
    if ($PSBoundParameters.Credential){
        $Params.Credential = $Credential
    }

    $Results = Invoke-Command -ComputerName $ComputerName {
        Get-ItemProperty -Path 'HKLM:\SOFTWARE\ESET\ESET Security\CurrentVersion\Info' -ErrorAction SilentlyContinue
    } @Params

    foreach ($Result in $Results) {
        [pscustomobject]@{
            ComputerName = $Result.PSComputerName
            ProductName = $Result.ProductName
            ScannerVersion = $Result.ScannerVersionId
            LastUpdate = if ($Result.ScannerVersion) {([datetime]::ParseExact($Result.ScannerVersion -replace '^.*\(|\)', 'yyyyMMdd', $null)).ToShortDateString()}
        }
    }


}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/01/eset-version2b.png"><img class="alignnone size-full wp-image-14899" src="http://mikefrobbins.com/wp-content/uploads/2017/01/eset-version2b.png" alt="" width="859" height="141" /></a>

In the previous example, Srv02 actually returns an error because the registry key doesn't exist and that error is suppressed with <em>-ErrorAction SilentlyContinue</em>.

One of two things need to happen to make Srv02 return a result so the PSComputerName synthetic property can be used in the output. Either catch the error and generate something out of nothing that can be returned as shown in the following example:
<pre class="lang:ps decode:true">function Get-MrEsetUpdateVersion {
    [CmdletBinding()]
    param (
        [ValidateNotNullOrEmpty()]
        [string[]]$ComputerName = $env:COMPUTERNAME,
        
        [System.Management.Automation.Credential()]$Credential = [System.Management.Automation.PSCredential]::Empty
    )

    $Params = @{}
    if ($PSBoundParameters.Credential){
        $Params.Credential = $Credential
    }

    $Results = Invoke-Command -ComputerName $ComputerName {
    try {
        Get-ItemProperty -Path 'HKLM:\SOFTWARE\ESET\ESET Security\CurrentVersion\Info' -ErrorAction Stop
    }
    catch {
        Write-Output ''
    }
            
    } @Params

    foreach ($Result in $Results) {
        [pscustomobject]@{
            ComputerName = $Result.PSComputerName
            ProductName = $Result.ProductName
            ScannerVersion = $Result.ScannerVersionId
            LastUpdate = if ($Result.ScannerVersion) {([datetime]::ParseExact($Result.ScannerVersion -replace '^.*\(|\)', 'yyyyMMdd', $null)).ToShortDateString()}
        }
    }


}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/01/eset-version3b.png"><img class="alignnone size-full wp-image-14900" src="http://mikefrobbins.com/wp-content/uploads/2017/01/eset-version3b.png" alt="" width="859" height="151" /></a>

Or return the error as part of the success output stream:
<pre class="lang:ps decode:true ">function Get-MrEsetUpdateVersion {
    [CmdletBinding()]
    param (
        [ValidateNotNullOrEmpty()]
        [string[]]$ComputerName = $env:COMPUTERNAME,
        
        [System.Management.Automation.Credential()]$Credential = [System.Management.Automation.PSCredential]::Empty
    )

    $Params = @{}
    if ($PSBoundParameters.Credential){
        $Params.Credential = $Credential
    }

    $Results = Invoke-Command -ComputerName $ComputerName {
        Get-ItemProperty -Path 'HKLM:\SOFTWARE\ESET\ESET Security\CurrentVersion\Info' 2&gt;&amp;1 
    } @Params

    foreach ($Result in $Results) {
        [pscustomobject]@{
            ComputerName = $Result.PSComputerName
            ProductName = $Result.ProductName
            ScannerVersion = $Result.ScannerVersionId
            LastUpdate = if ($Result.ScannerVersion) {([datetime]::ParseExact($Result.ScannerVersion -replace '^.*\(|\)', 'yyyyMMdd', $null)).ToShortDateString()}
        }
    }


}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/01/eset-version3b.png"><img class="alignnone size-full wp-image-14900" src="http://mikefrobbins.com/wp-content/uploads/2017/01/eset-version3b.png" alt="" width="859" height="151" /></a>

Notice that the date is now returned in a format that can be worked with. It can be used to return a list of all servers that haven't received updates in a certain number of days. The function shown in this blog article could also be used as one of the tests in your operational validation testing of your servers to verify that everything is configured and working properly as well as receiving proper updates for things like antivirus software.

The <em>Get-MrEsetUpdateVersion</em> function shown in this blog article can be downloaded from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository on GitHub</a>.

µ