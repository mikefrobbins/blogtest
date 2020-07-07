---
ID: 10875
post_title: >
  Use a certificate with PowerShell DSC to
  add a server to Active Directory without
  hard coding a password
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/12/04/use-a-certificate-with-powershell-dsc-to-add-a-server-to-active-directory-without-hard-coding-a-password/
published: true
post_date: 2014-12-04 07:30:07
---
A new Windows Server 2012 R2 machine has been brought online and needs to be joined to your Active Directory domain. All machines used in this demonstration are running either Windows Server 2012 R2 or Windows 8.1 with PowerShell version 4.

You've decided to use DSC (Desired State Configuration) to join this new server to the domain because it's a prototype for many more servers to come. You plan to automate their deployment along with the majority of their configuration with DSC.

While this blog article doesn't fully automate all of the process required to add the target node to the domain, it is meant to demonstrate the encryption and decryption of the password for the domain user account that is required to add it to the domain which would otherwise have to be stored in clear text.

The name of the target node has been added to the trusted host list on the machine that is being used to author and apply the DSC configuration:
<pre class="lang:ps decode:true">Set-Item -Path WSMan:\localhost\Client\TrustedHosts -Value 'WIN-U74LBSGF2CA' -Concatenate -Force</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/12/dsc-cert6.jpg"><img class="alignnone size-full wp-image-10903" src="http://mikefrobbins.com/wp-content/uploads/2014/12/dsc-cert6.jpg" alt="dsc-cert6" width="877" height="61" /></a>

The <a href="https://gallery.technet.microsoft.com/scriptcenter/xComputerManagement-Module-3ad911cc" target="_blank">xComputerManagment</a> module which is part of <a href="https://gallery.technet.microsoft.com/scriptcenter/xComputerManagement-Module-3ad911cc" target="_blank">DSC Resource Kit Wave 8</a>, contains the DSC resource that will be used to add our new server to the Active Directory domain. This module has been copied to "C:\Program Files\WindowsPowerShell\Modules" on the target node and the machine that will be used to author and apply the DSC configuration.

Fellow PowerShell MVP Vadims Podans has a <a href="https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6" target="_blank">PowerShell self-signed certificate generator</a> that was used to create a self-signed certificate on the target node:
<pre class="lang:ps decode:true">New-SelfSignedCertificateEx -Subject 'CN=WIN-U74LBSGF2CA' -StoreLocation LocalMachine -StoreName My</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/12/dsc-cert11.jpg"><img class="alignnone size-full wp-image-10947" src="http://mikefrobbins.com/wp-content/uploads/2014/12/dsc-cert11.jpg" alt="dsc-cert11" width="877" height="73" /></a>

Export the public key:
<pre class="lang:ps decode:true ">Export-Certificate -Cert Cert:\LocalMachine\My\27375EB88F2B13A20B58180497F5A7917208850A -FilePath C:\tmp\testcert.cer</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/12/dsc-cert10.jpg"><img class="alignnone size-full wp-image-10944" src="http://mikefrobbins.com/wp-content/uploads/2014/12/dsc-cert10.jpg" alt="dsc-cert10" width="877" height="190" /></a>

The public key that was exported in the previous step has been copied to the machine that will be used to author and apply the DSC configuration. The Thumbprint of the certificate has been entered into the DSC configuration as shown in the following example.

I had previously read an article on MSDN titled "<a href="http://blogs.msdn.com/b/visualstudioalm/archive/2014/07/22/deploying-using-powershell-desired-state-configuration-in-release-management.aspx" target="_blank">Deploying using PowerShell Desired State Configuration in Release Management</a>" that demonstrates using an initialization script so I thought I would take advantage of that technique here:
<pre class="lang:ps decode:true ">$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'WIN-U74LBSGF2CA'
            MachineName = 'DC02'
            Domain = 'mikefrobbins'
            CertificateFile = 'C:\tmp\testcert.cer'
            Thumbprint = '27375EB88F2B13A20B58180497F5A7917208850A'
        }
    )
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/11/dsc-cert1.jpg"><img class="alignnone size-full wp-image-10878" src="http://mikefrobbins.com/wp-content/uploads/2014/11/dsc-cert1.jpg" alt="dsc-cert1" width="883" height="199" /></a>

The LCM (Local Configuration Manager) on the target node needs to be made aware of the certificate that will be used to decrypt the password. That portion of the configuration could be split out into a separate configuration. I also set the LCM on the target node to automatically reboot the machine if a restart is required by configuration changes:
<pre class="lang:ps decode:true">Configuration MrDscConfiguration {

    param (
        [pscredential]$Credential 
    ) 
    
    Import-DscResource -Module xComputerManagement
    
    Node $AllNodes.NodeName {
        LocalConfigurationManager {
            CertificateID = $Node.Thumbprint
            RebootNodeIfNeeded = $true
        }

        xComputer JoinDomain {
            Name = $Node.MachineName  
            DomainName = $Node.Domain 
            Credential = $Credential 
        }
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/11/dsc-cert2.jpg"><img class="alignnone size-full wp-image-10879" src="http://mikefrobbins.com/wp-content/uploads/2014/11/dsc-cert2.jpg" alt="dsc-cert2" width="883" height="319" /></a>

When the configuration is run, two MOF files are created. One for the LCM and the other for the node configuration that you typically see created with DSC. The credential that is specified with this command is a domain account that has the necessary rights to add the machine to the domain. The password for this credential is what will be encrypted in the MOF file that is created:
<pre class="lang:ps decode:true ">MrDscConfiguration –ConfigurationData $ConfigData -Credential (Get-Credential) –Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/11/dsc-cert3.jpg"><img class="alignnone size-full wp-image-10880" src="http://mikefrobbins.com/wp-content/uploads/2014/11/dsc-cert3.jpg" alt="dsc-cert3" width="883" height="248" /></a>

The following example shows the contents of the WIN-U74LBSGF2CA.mof file:
<pre class="lang:ps decode:true  " title="WIN-U74LBSGF2CA.mof">/*
@TargetNode='WIN-U74LBSGF2CA'
@GeneratedBy=administrator
@GenerationDate=11/27/2014 16:56:46
@GenerationHost=PC01
*/

instance of MSFT_Credential as $MSFT_Credential1ref
{
Password = "MTxMxpE2+pivknezEqvHMGUTCsvENbeRC6AymAW1hDEyW/RCFZElL4tKugzsO2MksyVbUiDbq4xFVReVso2g6KVASDxQfxSK+NsjxlD4r4lvhYXfWAcNJaY748KPuESxYP/bdhb8IcOiLC2+3Zw666yLHGuBhfJuAUSrk2JTG9E=";
 UserName = "mikefrobbins\\administrator";

};

instance of MSFT_xComputer as $MSFT_xComputer1ref
{
ResourceID = "[xComputer]JoinDomain";
 Credential = $MSFT_Credential1ref;
 DomainName = "mikefrobbins";
 SourceInfo = "::11::9::xComputer";
 Name = "DC02";
 ModuleName = "xComputerManagement";
 ModuleVersion = "1.2";

};

instance of OMI_ConfigurationDocument
{
 Version="1.0.0";
 Author="administrator";
 GenerationDate="11/27/2014 16:56:46";
 GenerationHost="PC01";
 ContentType="PasswordEncrypted";
};</pre>
Notice that the password is encrypted in the mof file as shown the previous example. That is the whole point of this blog article. Don't store passwords in plain text &lt;period&gt;.

This example shows the contents of the WIN-U74LBSGF2CA.meta.mof file:
<pre class="lang:ps decode:true" title="WIN-U74LBSGF2CA.meta.mof">/*
@TargetNode='WIN-U74LBSGF2CA'
@GeneratedBy=administrator
@GenerationDate=11/27/2014 16:56:46
@GenerationHost=PC01
*/

instance of MSFT_DSCMetaConfiguration as $MSFT_DSCMetaConfiguration1ref
{
CertificateID = "27375EB88F2B13A20B58180497F5A7917208850A";
 RebootNodeIfNeeded = True;

};

instance of OMI_ConfigurationDocument
{
 Version="1.0.0";
 Author="administrator";
 GenerationDate="11/27/2014 16:56:46";
 GenerationHost="PC01";
};</pre>
Now to apply the changes to the LCM on the target node. The credential that is specified with this command is a local userid and password that has the necessary rights to perform these configuration changes on the target node:
<pre class="lang:ps decode:true ">Set-DscLocalConfigurationManager -Path .\MrDscConfiguration -Credential (Get-Credential) -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/11/dsc-cert4.jpg"><img class="alignnone size-full wp-image-10882" src="http://mikefrobbins.com/wp-content/uploads/2014/11/dsc-cert4.jpg" alt="dsc-cert4" width="883" height="294" /></a>

Why didn't I use a domain account in the previous example? Because the machine isn't yet a member of any Active Directory domain. It's a brand new server that's in a workgroup.

Now to apply the DSC configuration to the new server (our target node). Once again, we need to specify a local userid and password on the target node when prompted for credentials with this command. I could have previously stored the credentials in a variable and specified the variable since this is the second time I've had to type them in:
<pre class="lang:ps decode:true  ">Start-DscConfiguration -Wait -Path .\MrDscConfiguration -Credential (Get-Credential) -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/11/dsc-cert5.jpg"><img class="alignnone size-full wp-image-10883" src="http://mikefrobbins.com/wp-content/uploads/2014/11/dsc-cert5.jpg" alt="dsc-cert5" width="883" height="378" /></a>

If you happen to be signed into the target node when the configuration is applied, you'll receive the following message and about two seconds later the machine will restart:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/12/dsc-cert7.jpg"><img class="alignnone size-full wp-image-10915" src="http://mikefrobbins.com/wp-content/uploads/2014/12/dsc-cert7.jpg" alt="dsc-cert7" width="703" height="142" /></a>

Once the restart of the target node is complete, it will have been renamed and added to the domain just like magic and all without needing to store any passwords in plain text!

<span style="text-decoration: underline;">Troubleshooting notes:</span>

If you receive this error, there's a problem with the certificate you're using. I ended up with this error when I used a certificate that was created with <em>New-SelfSignedCertificate.</em>

<span style="color: #ff0000;">The private key could not be acquired.</span>
<span style="color: #ff0000;"> + CategoryInfo : NotSpecified: (root/Microsoft/...gurationManager:String) [], CimException</span>
<span style="color: #ff0000;"> + FullyQualifiedErrorId : MI RESULT 1</span>
<span style="color: #ff0000;"> + PSComputerName : WIN-U74LBSGF2CA</span>

<a href="http://mikefrobbins.com/wp-content/uploads/2014/12/dsc-cert8.jpg"><img class="alignnone size-full wp-image-10921" src="http://mikefrobbins.com/wp-content/uploads/2014/12/dsc-cert8.jpg" alt="dsc-cert8" width="877" height="264" /></a>

If you receive this error, you could have a certificate mismatch problem:

<span style="color: #ff0000;">Access is denied.</span>
<span style="color: #ff0000;"> + CategoryInfo : PermissionDenied: (root/Microsoft/...gurationManager:String) [], CimException</span>
<span style="color: #ff0000;"> + FullyQualifiedErrorId : HRESULT 0x80070005</span>
<span style="color: #ff0000;"> + PSComputerName : WIN-U74LBSGF2CA</span>

<a href="http://mikefrobbins.com/wp-content/uploads/2014/12/dsc-cert9.jpg"><img class="alignnone size-full wp-image-10922" src="http://mikefrobbins.com/wp-content/uploads/2014/12/dsc-cert9.jpg" alt="dsc-cert9" width="877" height="229" /></a>

When I encountered the certificate mismatch problem, it was because I had initially used a certificate that wouldn't work and then when I generated another certificate, I hadn't reconfigured the LCM on the target node to use it so the encryption was occurring with one certificate and the decryption was attempting to use a different certificate.

µ