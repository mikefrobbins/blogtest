---
ID: 10825
post_title: >
  Use PowerShell to Install Active
  Directory Certificate Services
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/11/20/use-powershell-to-install-active-directory-certificate-services/
published: true
post_date: 2014-11-20 07:30:36
---
In this blog article, I'll use PowerShell to install Active Directory Certificate Services in my test environment. The domain controller that's being used is running Windows Server 2012 R2 Server Core Installation (no-GUI). The workstation that I'm using is running Windows 8.1 and it is a member of the same Active Directory domain.

Many times when I'm prototyping something on a single remote server, I'll use one to one remoting so that it's an interactive session. The <em>Enter-PSSession</em> cmdlet is used for one to one remoting as shown in the following example where I'll establish an interactive session to my test domain controller named dc01:
<pre class="lang:ps decode:true">Enter-PSSession -ComputerName dc01</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/11/one-to-one-remoting.jpg"><img class="alignnone size-full wp-image-10826" src="http://mikefrobbins.com/wp-content/uploads/2014/11/one-to-one-remoting.jpg" alt="one-to-one-remoting" width="877" height="61" /></a>

Notice that the prompt is preceded by [dc01]: once the PowerShell remoting interactive session is established. Any commands that are issued while in an interactive session are executed on the remote computer that's specified in those square brackets.

In the previous example, I was logged into the workstation that I established the interactive session from as a user who has rights to establish an interactive session on dc01, but that probably wouldn't be the case in a production environment where you would need to use the <em>-Credential</em> parameter to specify alternate credentials.

Before jumping in head first and trying to install something, first check to see if it's already installed. In this case, I'll check to see if certificate services is already installed:
<pre class="lang:ps decode:true">Get-WindowsFeature -Name AD-Certificate</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/11/certificate-services1.jpg"><img class="alignnone size-full wp-image-10828" src="http://mikefrobbins.com/wp-content/uploads/2014/11/certificate-services1.jpg" alt="certificate-services1" width="877" height="132" /></a>

To install Active Directory Certificate Services, simply pipe the previous command to the <em>Install-WindowsFeature</em> cmdlet:
<pre class="lang:ps decode:true">Get-WindowsFeature -Name AD-Certificate | Install-WindowsFeature</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/11/certificate-services2.jpg"><img class="alignnone size-full wp-image-10829" src="http://mikefrobbins.com/wp-content/uploads/2014/11/certificate-services2.jpg" alt="certificate-services2" width="877" height="157" /></a>

There are a number of PowerShell cmdlets for deploying and administering Active Directory Certificate Services:
<pre class="lang:ps decode:true">Get-Command -Module ADCS*</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/11/certificate-services3.jpg"><img class="alignnone size-full wp-image-10831" src="http://mikefrobbins.com/wp-content/uploads/2014/11/certificate-services3.jpg" alt="certificate-services3" width="877" height="407" /></a>

One of the gotcha's about working in a PowerShell remoting interactive session is that the <em>-ShowWindow</em> parameter of <em>Get-Help</em> doesn't work:
<pre class="lang:ps decode:true">help Install-AdcsCertificationAuthority -ShowWindow</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/11/certificate-services4.jpg"><img class="alignnone size-full wp-image-10834" src="http://mikefrobbins.com/wp-content/uploads/2014/11/certificate-services4.jpg" alt="certificate-services4" width="877" height="144" /></a>

Specifying the <em>-WhatIf</em> parameter with <em>Install-AdcsCertificationAuthority</em> allows you to see the defaults that will be used without specifying any parameters (based on the help, no parameters are required):
<pre class="lang:ps decode:true">Install-AdcsCertificationAuthority -WhatIf</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/11/certificate-services5.jpg"><img class="alignnone size-full wp-image-10835" src="http://mikefrobbins.com/wp-content/uploads/2014/11/certificate-services5.jpg" alt="certificate-services5" width="877" height="334" /></a>

For this test environment, the defaults are fine so I'll run the cmdlet without <em>-WhatIf</em>:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/11/certificate-services6.jpg"><img class="alignnone size-full wp-image-10837" src="http://mikefrobbins.com/wp-content/uploads/2014/11/certificate-services6.jpg" alt="certificate-services6" width="877" height="192" /></a>

An ErrorId of 0 indicates that the role was installed successfully.

To exit the remote interactive session, simply use the <em>Exit-PSSession</em> cmdlet:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/11/certificate-services8.jpg"><img class="alignnone size-full wp-image-10840" src="http://mikefrobbins.com/wp-content/uploads/2014/11/certificate-services8.jpg" alt="certificate-services8" width="877" height="60" /></a>

µ