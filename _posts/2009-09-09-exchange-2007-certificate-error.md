---
ID: 32
post_title: Outlook Security Alert
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2009/09/09/exchange-2007-certificate-error/
published: true
post_date: 2009-09-09 20:36:42
---
Problem:
When opening Microsoft Outlook you receive a Security Alert  "Information you exchange with this site cannot be viewed or changed by others. However, there is a problem with the site's security certificate."

<img class="alignnone size-full wp-image-34" title="ExchangeCertificateError" src="http://mikefrobbins.com/wp-content/uploads/2009/09/exchangecertificateerror.png" alt="ExchangeCertificateError" width="379" height="301" />

Solution:
The self-signed certificate that was created during the Exchange 2007 installation expires after one year. Use Exchange Management Shell to validate this is the problem you're experiencing by running the following cmdlet.
<pre class="lang:ps decode:true">Get-ExchangeCertificate | Format-List</pre>
<em>NotAfter</em> shows the certificate expiration date, <em>Services</em> shows the mail services that are being used by a particular certificate, and <em>Thumbprint</em> will be used to resolve this problem if your certificate is indeed expired.

Use the Exchange Management Shell to renew your default self-signed certificate to resolve this problem if it is expired. Note, this procedure cannot be used to renew a certificate purchased from a trusted certificate authority.

First, obtain the thumbprint of the current default certificate by running the command shown in the previous example. Next, clone the current certificate by running the following command.
<pre class="lang:ps decode:true">Get-ExchangeCertificate -Thumbprint xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx | New-ExchangeCertificate</pre>
And finally, remove the expired certificate.
<pre class="lang:ps decode:true">Remove-ExchangeCertificate -Thumbprint xxOLDTHUMBPRINTxx</pre>
The new cloned certificate will be good for one year.

Solution #2:
You also have the option of creating a new self-signed certificate. Verify your certificate is expired and obtain its Thumbprint by using that portion of the above procedure. Create a new self-signed certificate by using the <em>New-ExchangeCertificate</em> cmdlet. You'll be prompted to replace the default certificate, choose yes. Associate the new certificate with IIS with the command shown in the following example unless you have purchased a certificate from a trusted certificate authority and it is associated with IIS.
<pre class="lang:ps decode:true">Enable-ExchangeCertificate -Thumbprint xxNEWTHUNBPRINTxx -Service IIS</pre>
Remove the expired certificate once it's no longer in use.

This option is less desirable than renewing the current certificate since any modifications made to the Exchange website SSL settings could be affected since SSL is removed, re-added, and reset as “Require 128-bit encryption”.

For more information:
<a href="http://technet.microsoft.com/en-us/library/bb851505.aspx" target="_blank" rel="noopener">http://technet.microsoft.com/en-us/library/bb851505.aspx</a>

µ