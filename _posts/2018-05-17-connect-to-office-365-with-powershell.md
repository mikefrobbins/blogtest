---
ID: 16510
post_title: Connect to Office 365 with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/05/17/connect-to-office-365-with-powershell/
published: true
post_date: 2018-05-17 07:30:23
---
I've recently been working on a project to migrate an Exchange Server 2010 environment to Office 365. As with Exchange, there are several things that simply can't be done from the GUI in Office 365. This means that if you're the Office 365 administrator for your company, you'll need a certain level of proficiency with PowerShell to effectively do your job .

While not requirements, this blog article is written using Windows 10 Enterprise Edition version 1803 and PowerShell Core version 6.0.2. All of the examples also work using the default version of Windows PowerShell that ships with Windows 10. Your mileage may vary with other operating systems and other versions of PowerShell.

The following three commands could be run using a PowerShell one-liner, but I find that it's easier to understand if they're broken down into separate commands.

First, store your Office 365 credentials with sufficient access to your Office 365 environment in a variable.
<pre class="lang:ps decode:true">$O365Cred = Get-Credential</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/05/office365login1a.png"><img class="alignnone size-full wp-image-16511" src="http://mikefrobbins.com/wp-content/uploads/2018/05/office365login1a.png" alt="" width="859" height="145" /></a>

Create a new PSSession to your Office 365 account.
<pre class="lang:ps decode:true ">$Session = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri https://ps.outlook.com/powershell-liveid?DelegatedOrg=yourdomain.onmicrosoft.com -Credential $O365Cred -Authentication Basic -AllowRedirection</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/05/office365login2a.png"><img class="alignnone size-full wp-image-16512" src="http://mikefrobbins.com/wp-content/uploads/2018/05/office365login2a.png" alt="" width="859" height="75" /></a>

Import the PSSession.
<pre class="lang:ps decode:true ">Import-PSSession -Session $Session</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/05/office365login3a.png"><img class="alignnone size-full wp-image-16513" src="http://mikefrobbins.com/wp-content/uploads/2018/05/office365login3a.png" alt="" width="859" height="185" /></a>

This creates a temporary module on your local system with a random name. Use <em>Get-Command</em> along with the name of the temporary module to determine the list of commands that were added.
<pre class="lang:ps decode:true">Get-Command -Module &lt;modulename&gt;</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/05/office365login4a.png"><img class="alignnone size-full wp-image-16514" src="http://mikefrobbins.com/wp-content/uploads/2018/05/office365login4a.png" alt="" width="859" height="410" /></a>

At this point, any of the commands from the temporary module can be run and used to manage your Office 365 environment.

This module will exist until PowerShell is closed or until you remove the PSSession.
<pre class="lang:ps decode:true">Remove-PSSession -Session $Session</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/05/office365login5a.png"><img class="alignnone size-full wp-image-16515" src="http://mikefrobbins.com/wp-content/uploads/2018/05/office365login5a.png" alt="" width="859" height="60" /></a>

Or at least until the PSSession has be idle for a certain amount of time. The default idle timeout is 15 minutes (900,000 milliseconds).
<pre class="lang:ps decode:true ">Get-PSSession | Select-Object -Property IdleTimeout</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/05/office365login6a.png"><img class="alignnone size-full wp-image-16516" src="http://mikefrobbins.com/wp-content/uploads/2018/05/office365login6a.png" alt="" width="859" height="143" /></a>

In the previous command, if more than one PSSession exists, narrow down the results using one of the parameters for <em>Get-PSSession</em> to obtain accurate results for the specific one that's established to Office 365.

µ