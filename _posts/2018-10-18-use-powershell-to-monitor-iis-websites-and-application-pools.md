---
ID: 17269
post_title: >
  Use PowerShell to Monitor IIS Websites
  and Application Pools
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/10/18/use-powershell-to-monitor-iis-websites-and-application-pools/
published: true
post_date: 2018-10-18 07:30:00
---
I recently received a request to write a script for monitoring IIS websites and their application pools on a specific Windows 2012 R2 server and start them if they were stopped. This is something I created fairly quickly and thought I would share.

I always try to centralize any scripts I write and have them run on a job server instead of the actual server they're querying. That way I don't have multiple versions of the same script floating around on different servers. I can easily use this same script on multiple servers by simply adding additional server names to the <em>$Servers</em> variable. The list of servers can be hard coded like I've done in this scenario, they could be retrieved from Active Directory, a text file, from a SQL Server database, or from almost any place you could imagine.

For some reason, the <em>WebAdministration</em> PowerShell module didn't seem to want to auto-load even though this particular server has Windows PowerShell version 4.0 installed. I had no problem manually importing it though.
<pre class="lang:ps decode:true">$Servers = 'Server01'
Invoke-Command -ComputerName $Servers {
    Import-Module -Name WebAdministration
    $Websites  = Get-Website | Where-Object serverAutoStart -eq $true
    foreach ($Website in $Websites) {
        switch ($Website) {
            {(Get-WebAppPoolState -Name $_.applicationPool).Value -eq 'Stopped'} {Start-WebAppPool -Name $_.applicationPool}
            {$_.State -eq 'Stopped'} {Start-Website -Name $Website.Name}
        }
    }
}</pre>
This script is fairly simple. It retrieves a list of websites that are set to start automatically. Originally, I had retrieved all of the websites and used an <em>If</em> statement inside the <em>Switch</em> statement to filter down to only the ones that were set to automatically start, but this is one scenario where it's more efficient and cleaner to filter when retrieving the list of websites using <em>Where-Object</em>. If <em>Get-Website</em> had a parameter for filtering on the <em>serverAutoStart</em> property, that would be even more efficient.

The logic is that if they're set to auto start, then they should be running. It checks to see if the application pool is started and starts it if not. Then it checks to see if the website is running and starts it if not. Like I said, simple.

I decided to use a <em>Switch</em> statement instead of several <em>If</em> statements and notice that it doesn't really matter what you put in the items for the switch statement. Regardless if they're related to the variable you're testing or not, if they evaluate to true, the code in them will run.

Set it up as a scheduled task to run at a periodic interval and you're all set. PowerShell eventing might also be another possibility instead of a scheduled task if you want the script to be a little smarter.

Depending on the version of Windows Server and/or IIS, you may have an IIS PowerShell module by a different name that contains different commands. You could also use the IIS WMI classes that exist in the <em><span class="crayon-i">root</span><span class="crayon-o">/</span><span class="crayon-i">MicrosoftIISv2 </span></em>namespace which require the IIS 6 WMI Compatibility component to be installed.
<pre class="lang:ps decode:true ">Install-WindowsFeature -Name Web-WMI</pre>
Using the <a href="https://www.powershellgallery.com/packages/Carbon/" target="_blank" rel="noopener">Carbon (third party) PowerShell module</a> is another possibility. If you happen to go this route, be sure to take a look at the methods on the <em>Get-IisWebsite</em> and <em>Get-IisAppPool</em> commands as there aren't dedicated commands to start websites or app pools, but there are methods to accomplish these actions.

As always, let me know what you think. Please post your questions, comments, and/or suggestions as a comment to this blog article.

µ