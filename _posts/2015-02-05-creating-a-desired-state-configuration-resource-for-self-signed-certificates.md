---
ID: 11159
post_title: >
  Creating a Desired State Configuration
  Resource for Self Signed Certificates
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/02/05/creating-a-desired-state-configuration-resource-for-self-signed-certificates/
published: true
post_date: 2015-02-05 07:30:14
---
For those of you who follow my blog, you know that I've been working on using DSC (Desired State Configuration) to fully automate the build of my test environment that runs as Hyper-V VM's on my Windows 8.1 computer.

Last week in my blog article titled "<a href="http://mikefrobbins.com/2015/01/29/automate-the-installation-of-dsc-resource-kit-wave-9-resources-with-powershell-desired-state-configuration/" target="_blank">Automate the installation of DSC Resource Kit Wave 9 resources with PowerShell Desired State Configuration</a>", I demonstrated how to do just that, automate the installation of the Microsoft created DSC resources that are part of the most recent DSC resource kit (wave 9). For more details on the current state of my test environment, see that previous blog article.

The first VM in my test environment is named Test01 and it will become the first domain controller in my test environment. That will require passwords to either be stored in clear text or a certificate to be created on Test01 that can be used to encrypt the passwords. I've previously written about that process in another blog article titled “<a href="http://mikefrobbins.com/2014/12/04/use-a-certificate-with-powershell-dsc-to-add-a-server-to-active-directory-without-hard-coding-a-password/" target="_blank">Use a certificate with PowerShell DSC to add a server to Active Directory without hard coding a password</a>”, but I needed a more automated solution.

At first, I tried using the Script resource which I used in last weeks blog article, but that ended up being a less than desirable solution so I figured I would write a DSC resource which would move the complexity from the DSC configuration to a DSC resource. That way a person who is less skilled with DSC could write the configuration if needed.

I'm not going to go into quite as much detail about all of the steps to create a DSC resource in this blog article since I've previously written a <a href="http://mikefrobbins.com/2014/10/02/creating-a-custom-dsc-resource-to-configure-jumbo-frames-on-network-cards/" target="_blank">blog article</a> that does that.

First, I create the skeleton of my new DSC resource with a PowerShell one-liner:
<pre class="lang:ps decode:true ">New-xDscResource –Name cMrSelfSignedCert -Property (
    New-xDscResourceProperty –Name Subject –Type String –Attribute Key), (
    New-xDscResourceProperty –Name StoreLocation –Type String –Attribute Write –ValidateSet 'CurrentUser', 'LocalMachine'), (
    New-xDscResourceProperty –Name StoreName –Type String –Attribute Write –ValidateSet 'TrustedPublisher',
                                                                                        'ClientAuthIssuer',
                                                                                        'Root',
                                                                                        'CA',
                                                                                        'AuthRoot',
                                                                                        'TrustedPeople',
                                                                                        'My',
                                                                                        'SmartCardRoot',
                                                                                        'Trust',
                                                                                        'Disallowed'), (
    New-xDscResourceProperty –Name Ensure –Type String –Attribute Write –ValidateSet 'Absent', 'Present'
) -Path "$env:ProgramFiles\WindowsPowershell\Modules\cMrSelfSignedCert"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert1a.jpg"><img class="alignnone size-full wp-image-11161" src="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert1a.jpg" alt="dscresource-cert1a" width="877" height="562" /></a>

The previous step creates the directory structure, a PowerShell script module (psm1 file) and a MOF file.

There are three functions that the PSM1 file of your DSC resource must contain. <em>Get-TargetResource</em> which must return a hash table. <em>Set-TargetResource</em> which performs the action to bring whatever you're configuring into compliance, and <em>Test-TargetResource</em> which must return a Boolean as shown in the following example:
<pre class="lang:ps decode:true ">function Get-TargetResource {
	[CmdletBinding()]
	[OutputType([Hashtable])]
	param (
		[parameter(Mandatory)]
		[String]$Subject,

        [parameter(Mandatory)]
		[ValidateSet('CurrentUser','LocalMachine')]
		[String]$StoreLocation,
        
        [parameter(Mandatory)]
		[ValidateSet('TrustedPublisher','ClientAuthIssuer','Root','CA','AuthRoot','TrustedPeople','My','SmartCardRoot','Trust','Disallowed')]
		[String]$StoreName
	)

    $certInfo = Get-ChildItem -Path "Cert:\$StoreLocation\$StoreName" |
                Where-Object Subject -eq $Subject

    if ($certInfo) {
        Write-Verbose -Message "The certificate with subject: $Subject exists."
        $ensureResult = 'Present'
    }
    else {
        Write-Verbose -Message "The certificate with subject: $Subject does not exist."
        $ensureResult = 'Absent'
    }
    
    $returnValue = @{
        Subject  = $certInfo.Subject
        Ensure = $ensureResult
    }

    $returnValue
}

function Set-TargetResource {
	[CmdletBinding()]
	param (
		[parameter(Mandatory)]
		[String]$Subject,
        
        [parameter(Mandatory)]
		[ValidateSet('CurrentUser','LocalMachine')]
		[String]$StoreLocation,
        
        [parameter(Mandatory)]
		[ValidateSet('TrustedPublisher','ClientAuthIssuer','Root','CA','AuthRoot','TrustedPeople','My','SmartCardRoot','Trust','Disallowed')]
		[String]$StoreName,
        
        [parameter(Mandatory)]
		[ValidateSet('Absent','Present')]
		[String]$Ensure
	)

    if ($Ensure -eq 'Present') {

        if (-not(Get-Module -Name MrCertificate -ListAvailable)) {

            $Uri = 'https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/file/101251/1/New-SelfSignedCertificateEx.zip'
            $ModulePath = "$env:ProgramFiles\WindowsPowerShell\Modules\MrCertificate"
            $OutFile = "$env:ProgramFiles\WindowsPowerShell\Modules\MrCertificate\New-SelfSignedCertificateEx.zip"
            
            Write-Verbose -Message 'Required module MrCertificate does not exist and will now be installed.'
            New-Item -Path $ModulePath -ItemType Directory

            Write-Verbose -Message 'Downloading the New-SelfSignedCertificateEx.zip file from the TechNet script repository.'
            Invoke-WebRequest -Uri $Uri -OutFile $OutFile
            Unblock-File -Path $OutFile

            Write-Verbose -Message 'Extracting the New-SelfSignedCertificateEx.zip file to the MrCertificate module folder.'
            Add-Type -AssemblyName System.IO.Compression.FileSystem
            [System.IO.Compression.ZipFile]::ExtractToDirectory($OutFile, $ModulePath)

            Write-Verbose -Message 'Creating the mrcertificate.psm1 file and adding the necessary content to it.'
            New-Item -Path $ModulePath -Name mrcertificate.psm1 -ItemType File |
            Add-Content -Value '. "$env:ProgramFiles\WindowsPowerShell\Modules\MrCertificate\New-SelfSignedCertificateEx.ps1"'
            
            Remove-Item -Path $OutFile -Force
        }
               
        Import-Module -Name MrCertificate

        Write-Verbose -Message "Creating certificate with subject: $Subject" 
        New-SelfSignedCertificateEx -Subject $Subject -StoreLocation $StoreLocation -StoreName $StoreName    
    }
    elseif ($Ensure -eq 'Absent') {

        Write-Verbose -Message "Removing the certificate with subject $Subject."
        Get-ChildItem -Path "Cert:\$StoreLocation\$StoreName" |
        Where-Object Subject -eq $Subject |
        Remove-Item -Force    
    }
}

function Test-TargetResource {
	[CmdletBinding()]
	[OutputType([Boolean])]
	param (
		[parameter(Mandatory)]
		[String]$Subject,
        
        [parameter(Mandatory)]
		[ValidateSet('CurrentUser','LocalMachine')]
		[String]$StoreLocation,

        [parameter(Mandatory)]
		[ValidateSet('TrustedPublisher','ClientAuthIssuer','Root','CA','AuthRoot','TrustedPeople','My','SmartCardRoot','Trust','Disallowed')]
		[String]$StoreName,

        [parameter(Mandatory)]
		[ValidateSet('Absent','Present')]
		[String]$Ensure
	)

    $certInfo = Get-ChildItem -Path "Cert:\$StoreLocation\$StoreName" |
                Where-Object Subject -eq $Subject    
    
    switch ($Ensure) {
        'Absent' {$DesiredSetting = $false}
        'Present' {$DesiredSetting = $true}
    }

    if (($certInfo -and $DesiredSetting) -or (-not($certInfo) -and -not($DesiredSetting))) {
         Write-Verbose -Message 'The certificate matches the desired state.'
         [Boolean]$result = $true
    }
    else {
        Write-Verbose -Message 'The certificate does not match the desired state.'
        [Boolean]$result = $false
    }
	
	$result
}

Export-ModuleMember -Function *-TargetResource</pre>
I will tell you, the code you see in the PSM1 file shown in the previous example isn't your ordinary DSC resource example because it does something brilliant or insane depending on how you look at it and whether or not your a half full or half empty kind of person.

As noted in one of my blog articles that was previously referenced, fellow PowerShell MVP Vadims Podans has a <a href="https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6" target="_blank">PowerShell self-signed certificate generator</a> function that I wanted to use to simplify the process of creating the actual certificate. His function is contained in a signed PS1 file which I didn't want to modify so my DSC resource simply downloads his function as a zip file from the TechNet script repository, extracts it, and creates a module named <em>MrCertificate</em> that does nothing more than dot source his PS1 script file.

I create a module manifest (psd1 file) for my new DSC resource:
<pre class="lang:ps decode:true">New-ModuleManifest -Path "$env:ProgramFiles\WindowsPowershell\Modules\cMrSelfSignedCert\cMrSelfSignedCert.psd1" -Author 'Mike F Robbins' -CompanyName 'mikefrobbins.com' -RootModule 'cMrSelfSignedCert' -Description 'Module to Create Self Signed Certificates' -PowerShellVersion 4.0 -FunctionsToExport '*.TargetResource' -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert2a.jpg"><img class="alignnone size-full wp-image-11163" src="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert2a.jpg" alt="dscresource-cert2a" width="877" height="120" /></a>

Using a simple DSC configuration:
<pre class="lang:ps decode:true ">Configuration CreateSelfSignedCert {

    param (
        [Parameter(Mandatory)]
        [string[]]$ComputerName
    )
    
    Import-DscResource -ModuleName cMrSelfSignedCert
    
    node $ComputerName {
 
        cMrSelfSignedCert CreateCert {
            Subject = 'CN=192.168.29.108'  
            StoreLocation = 'LocalMachine' 
            StoreName = 'My'
            Ensure = 'Present'
        }
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert3a.jpg"><img class="alignnone size-full wp-image-11166" src="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert3a.jpg" alt="dscresource-cert3a" width="877" height="289" /></a>

A MOF file is created:
<pre class="lang:ps decode:true">CreateSelfSignedCert -ComputerName '192.168.29.108'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert4a.jpg"><img class="alignnone size-full wp-image-11167" src="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert4a.jpg" alt="dscresource-cert4a" width="877" height="180" /></a>

When the configuration is applied, a self signed certificate is created on my Test01 VM which can be used to encrypt the password that is contained in the MOF file of future configurations that I plan to create:
<pre class="lang:ps decode:true ">Start-DscConfiguration -Wait -Path .\CreateSelfSignedCert -Credential (Get-Credential) -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert5a.jpg"><img class="alignnone size-full wp-image-11168" src="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert5a.jpg" alt="dscresource-cert5a" width="877" height="552" /></a>

I'll use a PowerShell one-to-one remoting session to confirm the certificate was created:
<pre class="lang:ps decode:true ">Get-ChildItem -Path Cert:\LocalMachine\My</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert6a.jpg"><img class="alignnone size-full wp-image-11170" src="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert6a.jpg" alt="dscresource-cert6a" width="877" height="180" /></a>

Reapplying the configuration shows that the certificate already exists and nothing needs to be done so the set portion of the configuration is skipped:
<pre class="lang:ps decode:true ">Start-DscConfiguration -Wait -Path .\CreateSelfSignedCert -Credential (Get-Credential) -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert7a.jpg"><img class="alignnone size-full wp-image-11172" src="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert7a.jpg" alt="dscresource-cert7a" width="877" height="286" /></a>

I'll test removing the certificate by changing <em>Ensure</em> in the configuration to <em>Absent</em>:
<pre class="lang:ps decode:true ">Configuration CreateSelfSignedCert {

    param (
        [Parameter(Mandatory)]
        [string[]]$ComputerName
    )
    
    Import-DscResource -ModuleName cMrSelfSignedCert
    
    node $ComputerName {
 
        cMrSelfSignedCert CreateCert {
            Subject = 'CN=192.168.29.108'  
            StoreLocation = 'LocalMachine' 
            StoreName = 'My'
            Ensure = 'Absent'
        }
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert8a.jpg"><img class="alignnone size-full wp-image-11174" src="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert8a.jpg" alt="dscresource-cert8a" width="877" height="288" /></a>

The MOF file has to be recreated since the configuration was changed:
<pre class="lang:ps decode:true ">CreateSelfSignedCert -ComputerName '192.168.29.108'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert9a.jpg"><img class="alignnone size-full wp-image-11175" src="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert9a.jpg" alt="dscresource-cert9a" width="877" height="177" /></a>

As you can see, applying this new configuration does indeed remove the certificate:
<pre class="lang:ps decode:true">Start-DscConfiguration -Wait -Path .\CreateSelfSignedCert -Credential (Get-Credential) -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert10a.jpg"><img class="alignnone size-full wp-image-11176" src="http://mikefrobbins.com/wp-content/uploads/2015/02/dscresource-cert10a.jpg" alt="dscresource-cert10a" width="877" height="324" /></a>

µ