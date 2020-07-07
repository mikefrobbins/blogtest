---
ID: 6624
post_title: >
  PowerShell Remoting Insanity with
  AppAssure and the Invoke-Command Cmdlet
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/01/31/powershell-remoting-insanity-with-appassure-and-the-invoke-command-cmdlet/
published: true
post_date: 2013-01-31 07:30:52
---
Although simple, I thought I would share the following PowerShell script since it's neat due to the fact that it uses the PowerShell remoting <em>Invoke-Command</em> cmdlet to retrieve a list of server names that are protected by an AppAssure Core server where the status is not protected, then it uses the output of that portion of the script (Nesting in parenthesis) as the input for the ComputerName parameter of the outer portion of the script which uses another <em>Invoke-Command</em> to attempt to start the AppAssureAgent service on those servers that were returned by the nested portion of the script:
<pre class="lang:ps decode:true">$cred = Get-Credential
Invoke-Command -ComputerName (
Invoke-Command -ComputerName AppAssureCoreServerName {
Get-ProtectedServers |
where status -ne protected |
select -expand displayname
} -Credential $cred) {
Start-Service AppAssureAgent -PassThru} -Credential $cred</pre>
<img class="alignnone size-full wp-image-6625" alt="appassure-icm" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure-icm.png" width="640" height="260" />

AppAssure version 5.3.1 or higher is required on the AppAssure Core server for PowerShell support. I've used the simplified PowerShell version 3 <em>Where-Object</em> syntax in this script since the AppAssure Core server is running PowerShell version 3 .<em id="__mceDel" style="color: #444444;">
</em>

µ