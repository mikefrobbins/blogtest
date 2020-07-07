---
ID: 11200
post_title: >
  Using PowerShell Desired State
  Configuration to build the first domain
  controller in your Active Directory
  forest
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/02/12/using-powershell-desired-state-configuration-to-build-the-first-domain-controller-in-your-active-directory-forest/
published: true
post_date: 2015-02-12 07:30:18
---
If you're a frequent reader of the blog articles on this site, then you know that I've been working on using Desired State Configuration to build my test lab environment that runs as Hyper-V VM's on my Windows 8.1 computer.

If you would like to know the current state of my test environment, see the previous blog article: "<a href="http://mikefrobbins.com/2015/02/05/creating-a-desired-state-configuration-resource-for-self-signed-certificates/" target="_blank">Creating a Desired State Configuration Resource for Self Signed Certificates</a>".

The certificate created in last week's blog has been exported and copied to the Windows 8.1 computer that I'm using to author and apply the DSC configuration from. This is a step that I'll need to revisit at some point in the future and take a look at automating it.
<pre class="lang:ps decode:true">Invoke-Command -ComputerName '192.168.29.108' {
    Export-Certificate -Cert Cert:\LocalMachine\My\$((Get-ChildItem -Path Cert:\LocalMachine\My |
    Where-Object Subject -eq 'CN=192.168.29.108').Thumbprint) -FilePath C:\tmp\192.168.29.108.cer

    Get-NetFirewallRule -DisplayGroup 'File and Printer Sharing' |
    Set-NetFirewallRule -Enabled True
} -Credential (Get-Credential)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/dc-dsc0a.jpg"><img class="alignnone size-full wp-image-11214" src="http://mikefrobbins.com/wp-content/uploads/2015/02/dc-dsc0a.jpg" alt="dc-dsc0a" width="877" height="311" /></a>

Today I'll be changing the name of the VM to Test01, changing the IP address from DHCP to a statically assigned one, and turning it into the first domain controller in my test Active Directory forest.

My initialization script contains all the values instead of hard coding them into the actual DSC configuration itself:
<pre class="lang:ps decode:true">$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '192.168.29.108'
            MachineName = 'Test01'
            DomainName = 'mrtest.local'
            IPAddress = '192.168.29.150'
            InterfaceAlias = 'Ethernet'
            DefaultGateway = '192.168.29.1'
            SubnetMask = '24'
            AddressFamily = 'IPv4'
            DNSAddress = '127.0.0.1', '192.168.29.150'
            CertificateFile = 'C:\tmp\192.168.29.108.cer'
            Thumbprint = '6229581CADD8EFCD6988172D379184A0606DB079'
        }
    )
}</pre>
A fairly simple DSC configuration is used to accomplish the tasks. As you can see, there are three DSC resources that are being used that don't ship in the box. Those were downloaded in another previous blog article: "<a href="http://mikefrobbins.com/2015/01/29/automate-the-installation-of-dsc-resource-kit-wave-9-resources-with-powershell-desired-state-configuration/" target="_blank">Automate the installation of DSC Resource Kit Wave 9 resources with PowerShell Desired State Configuration</a>".
<pre class="lang:ps decode:true">Configuration BuildTest01 {
    param (
        [Parameter(Mandatory)] 
        [pscredential]$safemodeCred, 
        
        [Parameter(Mandatory)] 
        [pscredential]$domainCred,

        [pscredential]$Credential
    )

    Import-DscResource -Module xActiveDirectory, xComputerManagement, xNetworking

    Node $AllNodes.NodeName {
        LocalConfigurationManager {
            CertificateID = $Node.Thumbprint
            RebootNodeIfNeeded = $true
        }
        xComputer SetName { 
          Name = $Node.MachineName 
        }
        xIPAddress SetIP {
            IPAddress = $Node.IPAddress
            InterfaceAlias = $Node.InterfaceAlias
            DefaultGateway = $Node.DefaultGateway
            SubnetMask = $Node.SubnetMask
            AddressFamily = $Node.AddressFamily
        }
        xDNSServerAddress SetDNS {
            Address = $Node.DNSAddress
            InterfaceAlias = $Node.InterfaceAlias
            AddressFamily = $Node.AddressFamily
        }
        WindowsFeature ADDSInstall {
            Ensure = 'Present'
            Name = 'AD-Domain-Services'
        }
        xADDomain FirstDC {
            DomainName = $Node.DomainName
            DomainAdministratorCredential = $domainCred
            SafemodeAdministratorPassword = $safemodeCred
            DependsOn = '[xComputer]SetName', '[xIPAddress]SetIP', '[WindowsFeature]ADDSInstall'
        }    
    }
}</pre>
The configuration is run which loads it into memory like a function, and then it is called with the necessary parameters and their values to create the MOF files:
<pre class="lang:ps decode:true">BuildTest01 –ConfigurationData $ConfigData -Credential (Get-Credential)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/dc-dsc1a.jpg"><img class="alignnone size-full wp-image-11203" src="http://mikefrobbins.com/wp-content/uploads/2015/02/dc-dsc1a.jpg" alt="dc-dsc1a" width="877" height="299" /></a>

We need to tell the test01 VM what the thumbprint of the certificate is that will be used to encrypt the passwords in the MOF file so it will know which one to use to decrypt them. We'll also change the RebootNodeIfNeeded to tell it to restart if needed. These changes are applied to the DSC LCM (Local Configuration Manager) on test01:
<pre class="lang:ps decode:true ">Set-DscLocalConfigurationManager -Path .\BuildTest01 -Credential (Get-Credential) -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/dc-dsc2a.jpg"><img class="alignnone size-full wp-image-11204" src="http://mikefrobbins.com/wp-content/uploads/2015/02/dc-dsc2a.jpg" alt="dc-dsc2a" width="877" height="276" /></a>

Now to apply (push) the actual configuration to test01:
<pre class="lang:ps decode:true ">Start-DscConfiguration -Wait -Path .\BuildTest01 -Credential (Get-Credential) -Verbose -Force</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/dc-dsc3a.jpg"><img class="alignnone size-full wp-image-11205" src="http://mikefrobbins.com/wp-content/uploads/2015/02/dc-dsc3a.jpg" alt="dc-dsc3a" width="877" height="349" /></a>

Notice that in the previous set of results, the computer name was changed and the machine rebooted before making the other configuration changes.

You might be wondering how your interactive session is going to reconnect and apply the remainder of the configuration? especially when you consider that both the computer name and IP address are going to be changed. How's it going to know what to connect back to? It's not. The MOF file was sent to the destination node and it will finish the configuration on its own. You won't see that in your interactive window and you'll need to give the machine a few minutes to change the IP address, and turn itself into a domain controller. Once those steps are complete, the machine will reboot again and it will be a domain controller:
<pre class="lang:ps decode:true ">Invoke-Command -ComputerName Test01 {
    Get-ADDomainController | Select-Object -Property Name, Domain, IPv4Address
} -Credential (Get-Credential)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/02/dc-dsc4a.jpg"><img class="alignnone size-full wp-image-11206" src="http://mikefrobbins.com/wp-content/uploads/2015/02/dc-dsc4a.jpg" alt="dc-dsc4a" width="877" height="261" /></a>

µ