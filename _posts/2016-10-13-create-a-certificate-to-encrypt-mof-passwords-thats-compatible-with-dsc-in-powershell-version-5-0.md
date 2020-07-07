---
ID: 14601
post_title: 'Create a Certificate to Encrypt MOF Passwords that&#8217;s Compatible with DSC in PowerShell version 5.0'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/10/13/create-a-certificate-to-encrypt-mof-passwords-thats-compatible-with-dsc-in-powershell-version-5-0/
published: true
post_date: 2016-10-13 07:30:42
---
I've previously written a blog article titled "<em><a href="http://mikefrobbins.com/2014/12/04/use-a-certificate-with-powershell-dsc-to-add-a-server-to-active-directory-without-hard-coding-a-password/" target="_blank">Use a certificate with PowerShell DSC to add a server to Active Directory without hard coding a password</a></em>" where I had created a certificate that was used to encrypt the password in a PowerShell version 4 DSC (Desired State Configuration) MOF file.

The same procedure in PowerShell v5 generates an error stating the certificate cannot be used for encryption:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/dsc-cert-error1a.png"><img class="alignnone size-full wp-image-14602" src="http://mikefrobbins.com/wp-content/uploads/2016/09/dsc-cert-error1a.png" alt="dsc-cert-error1a" width="859" height="624" /></a>

<span style="color: #ff0000;"><em>ConvertTo-MOFInstance : System.ArgumentException error processing property 'Password' OF TYPE 'MSFT_Credential':</em></span>
<span style="color: #ff0000;"><em>Certificate '6EBFB5C88AB4B8C9E3B8E30E88A5D071D6735464' cannot be used for encryption. Encryption certificates must</em></span>
<span style="color: #ff0000;"><em>contain the Data Encipherment or Key Encipherment key usage, and include the Document Encryption Enhanced Key Usage</em></span>
<span style="color: #ff0000;"><em>(1.3.6.1.4.1.311.80.1).</em></span>
<span style="color: #ff0000;"><em>At C:\WINDOWS\system32\WindowsPowerShell\v1.0\Modules\PSDesiredStateConfiguration\PSDesiredStateConfiguration.psm1:303</em></span>
<span style="color: #ff0000;"><em>char:13</em></span>
<span style="color: #ff0000;"><em>+ ConvertTo-MOFInstance MSFT_Credential $newValue</em></span>
<span style="color: #ff0000;"><em>+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~</em></span>
<span style="color: #ff0000;"><em> + CategoryInfo : InvalidOperation: (:) [Write-Error], InvalidOperationException</em></span>
<span style="color: #ff0000;"><em> + FullyQualifiedErrorId : FailToProcessProperty,ConvertTo-MOFInstance</em></span>
<span style="color: #ff0000;"><em>Write-NodeMOFFile : Invalid MOF definition for node 'SQL01': Exception calling "ValidateInstanceText" with "1"</em></span>
<span style="color: #ff0000;"><em>argument(s): "Syntax error:</em></span>
<span style="color: #ff0000;"><em> At line:11, char:1</em></span>
<span style="color: #ff0000;"><em> Buffer:</em></span>
<span style="color: #ff0000;"><em>T_Credential1ref</em></span>
<span style="color: #ff0000;"><em>{</em></span>
<span style="color: #ff0000;"><em>}^;</em></span>
<span style="color: #ff0000;"><em>in</em></span>
<span style="color: #ff0000;"><em>"</em></span>
<span style="color: #ff0000;"><em>At</em></span>
<span style="color: #ff0000;"><em>C:\WINDOWS\system32\WindowsPowerShell\v1.0\Modules\PSDesiredStateConfiguration\PSDesiredStateConfiguration.psm1:2275</em></span>
<span style="color: #ff0000;"><em>char:21</em></span>
<span style="color: #ff0000;"><em>+ ... Write-NodeMOFFile $Name $mofNode $Script:NodeInstanceAlia ...</em></span>
<span style="color: #ff0000;"><em>+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~</em></span>
<span style="color: #ff0000;"><em> + CategoryInfo : InvalidOperation: (:) [Write-Error], InvalidOperationException</em></span>
<span style="color: #ff0000;"><em> + FullyQualifiedErrorId : InvalidMOFDefinition,Write-NodeMOFFile</em></span>

<span style="color: #ff0000;"><em>Errors occurred while processing configuration 'test'.</em></span>
<span style="color: #ff0000;"><em>At</em></span>
<span style="color: #ff0000;"><em>C:\WINDOWS\system32\WindowsPowerShell\v1.0\Modules\PSDesiredStateConfiguration\PSDesiredStateConfiguration.psm1:3705</em></span>
<span style="color: #ff0000;"><em>char:5</em></span>
<span style="color: #ff0000;"><em>+ throw $ErrorRecord</em></span>
<span style="color: #ff0000;"><em>+ ~~~~~~~~~~~~~~~~~~</em></span>
<span style="color: #ff0000;"><em> + CategoryInfo : InvalidOperation: (test:String) [], InvalidOperationException</em></span>
<span style="color: #ff0000;"><em> + FullyQualifiedErrorId : FailToProcessConfiguration</em></span>

The solution to this problem is contained in the error message. "<em>Encryption certificates must contain the Data Encipherment or Key Encipherment key usage, and include the Document Encryption Enhanced Key Usage</em>".

As used in the previously referenced blog article, the <a href="https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6" target="_blank">PowerShell self-signed certificate generator</a> function that Fellow Microsoft MVP Vadims Podans wrote is used to create a certificate on the target node:
<pre class="lang:ps decode:true ">New-SelfSignedCertificateEx -Subject 'CN=SQL01' -StoreLocation LocalMachine -StoreName My -KeyUsage DataEncipherment, KeyEncipherment -EnhancedKeyUsage 'Document Encryption'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/dsc-cert-error2a.png"><img class="alignnone size-full wp-image-14604" src="http://mikefrobbins.com/wp-content/uploads/2016/09/dsc-cert-error2a.png" alt="dsc-cert-error2a" width="859" height="71" /></a>

Notice in the previous example that the <em>KeyUsage</em> and <em>EnhancedKeyUsage</em> parameters are specified along with the values from the previously referenced error message so this certificate is generated with the necessary requirements to be used for encryption with DSC in PowerShell version 5.0.

Determine the thumbprint of the certificate (it's the only one that's been created in the past two days):
<pre class="lang:ps decode:true ">Get-ChildItem -Path  Cert:\LocalMachine\My\ |
Where-Object NotBefore -gt (Get-Date).AddDays(-2)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/dsc-cert-error3a.png"><img class="alignnone size-full wp-image-14605" src="http://mikefrobbins.com/wp-content/uploads/2016/09/dsc-cert-error3a.png" alt="dsc-cert-error3a" width="859" height="180" /></a>

Export the certificate to a file:
<pre class="lang:ps decode:true ">Export-Certificate -Cert Cert:\LocalMachine\My\3652E4B2C9C885B4A93E81A07407008780D48356 -FilePath C:\tmp\sql01cert.cer</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/dsc-cert-error4a.png"><img class="alignnone size-full wp-image-14606" src="http://mikefrobbins.com/wp-content/uploads/2016/09/dsc-cert-error4a.png" alt="dsc-cert-error4a" width="859" height="192" /></a>

The exported certificate has been copied to the computer used for authoring the configuration.

Both the MOF and meta.MOF files now generate without a problem:
<pre class="lang:ps decode:true ">test -ConfigurationData $ConfigData -Credential (Get-Credential)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/dsc-cert-error5a.png"><img class="alignnone size-full wp-image-14608" src="http://mikefrobbins.com/wp-content/uploads/2016/09/dsc-cert-error5a.png" alt="dsc-cert-error5a" width="859" height="240" /></a>

The password is indeed encrypted:
<pre class="lang:ps decode:true ">Get-Content -Path C:\test\SQL01.mof |
Select-String -Pattern 'Password'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/dsc-cert-error6a.png"><img class="alignnone size-full wp-image-14609" src="http://mikefrobbins.com/wp-content/uploads/2016/09/dsc-cert-error6a.png" alt="dsc-cert-error6a" width="859" height="179" /></a>

If you've found an easier way to accomplish this task, I'd love to hear about it via a comment to this blog article.

µ