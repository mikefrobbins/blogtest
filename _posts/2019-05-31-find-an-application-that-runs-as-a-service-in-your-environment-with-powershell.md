---
ID: 17878
post_title: >
  Find an Application that runs as a
  Service in your Environment with
  PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/05/31/find-an-application-that-runs-as-a-service-in-your-environment-with-powershell/
published: true
post_date: 2019-05-31 07:30:04
---
I recently worked with a vendor to remove an old application that was no longer being used from a server in one of my customer's environments. While many applications may be left to die on the server long after they're needed, this particular one transmitted data to a partner company so it definitely needed to be removed.

The problem is the application was so old that no one knew which server of the hundreds of servers it was running on. The partner company was able to provide the display name of the service that the application runs as which made this problem fairly easy to resolve with PowerShell. For this scenario, I'll use "OpenSSH" as the display name of the service that I'm looking for.

First, you'll need a list of all the servers in your environment to query. While this can be obtained from Active Directory, I've found that it's easier to maintain a list of servers in a text file.

<em>Get-Service</em> has a <em>ComputerName</em> parameter, but I don't recommend using the <em>ComputerName</em> parameter on most cmdlets because they use older DCOM protocols that are typically blocked by firewalls and when querying multiple computers, they're queried serially instead of in parallel. If one is unreachable for some reason, it has to timeout before continuing to the next system which is slow and inefficient to say the least.
<pre class="lang:ps decode:true ">Get-Service -ComputerName sql08, sql12, sql14, sql16, sql17 -DisplayName '*OpenSSH*'</pre>
Instead, I recommend using the one to many PowerShell remoting <em>Invoke-Command</em> cmdlet and run the <em>Get-Service</em> command within its <em>ScriptBlock</em> parameter.
<pre class="lang:ps decode:true">Invoke-Command -ComputerName sql08, sql12, sql14, sql16, sql17 {Get-Service -DisplayName '*OpenSSH*'}</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/05/find-service1a.jpg"><img class="alignnone size-full wp-image-17882" src="https://mikefrobbins.com/wp-content/uploads/2019/05/find-service1a.jpg" alt="" width="859" height="160" /></a>

By default, you'll need to be an admin on the remote systems, but you can provide alternate credentials if necessary.
<pre class="lang:ps decode:true">$Cred = Get-Credential</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/05/find-service2a.jpg"><img class="alignnone size-full wp-image-17883" src="https://mikefrobbins.com/wp-content/uploads/2019/05/find-service2a.jpg" alt="" width="859" height="325" /></a>

Then those credentials can be used along with the <em>Credential</em> parameter.
<pre class="lang:ps decode:true">Invoke-Command -ComputerName sql08, sql12, sql14, sql16, sql17 {Get-Service -DisplayName '*OpenSSH*'} -Credential $Cred</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/05/find-service3a.jpg"><img class="alignnone size-full wp-image-17884" src="https://mikefrobbins.com/wp-content/uploads/2019/05/find-service3a.jpg" alt="" width="859" height="174" /></a>

I've created a text file with the server names in it and I'll query the file with <em>Get-Content</em>.
<pre class="lang:ps decode:true">Get-Content -Path C:\tmp\myservers.txt</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/05/find-service4a.jpg"><img class="alignnone size-full wp-image-17885" src="https://mikefrobbins.com/wp-content/uploads/2019/05/find-service4a.jpg" alt="" width="859" height="132" /></a>

I'll combine those two techniques, placing <em>Get-Content</em> inside of parentheses so that portion of the command runs first and provides its results as the values for the <em>ComputerName</em> parameter of <em>Invoke-Command</em>.
<pre class="lang:ps decode:true">Invoke-Command -ComputerName (Get-Content -Path C:\tmp\myservers.txt) {Get-Service -DisplayName '*OpenSSH*'} -Credential $Cred</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/05/find-service5a.jpg"><img class="alignnone size-full wp-image-17886" src="https://mikefrobbins.com/wp-content/uploads/2019/05/find-service5a.jpg" alt="" width="859" height="174" /></a>

As I previously mentioned, you can also retrieve the computer names from Active Directory. All of the servers that I've been querying exist in a "SQL Servers" OU (Organizational Unit) so I can simply query Active Directory for all of the computer names in that particular OU. This does require the RSAT tools (Remote Server Administration Tools) to be installed on your workstation. <a href="https://mikefrobbins.com/2018/10/03/use-powershell-to-install-the-remote-server-administration-tools-rsat-on-windows-10-version-1809/" target="_blank" rel="noopener noreferrer">As of Windows 10 version 1809, RSAT can be installed via Features on Demand</a>.
<pre class="lang:ps decode:true ">Invoke-Command -ComputerName (Get-ADComputer -Filter * -SearchBase 'OU=SQL Servers,OU=Test,DC=mikefrobbins,DC=com').Name {Get-Service -DisplayName '*OpenSSH*'} -Credential $Cred</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/05/find-service6a.jpg"><img class="alignnone size-full wp-image-17889" src="https://mikefrobbins.com/wp-content/uploads/2019/05/find-service6a.jpg" alt="" width="859" height="176" /></a>

Keep in mind that this is running on your workstation and querying all of the specified servers remotely. The results are returned as deserialized objects. By default, <em>Invoke-Command</em> will run in parallel on up to 32 remote systems at once and that number can be controlled with its <em>ThrottleLimit</em> parameter.

Thoughts, questions, comments? Please post them as a comment to this blog article.

µ