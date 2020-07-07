---
ID: 6493
post_title: >
  Using PowerShell to Determine AppAssure
  Agent Status and Version
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/01/10/using-powershell-to-determine-appassure-agent-status-and-version/
published: true
post_date: 2013-01-10 07:30:04
---
The <em>Get-ProtectedServers</em> PowerShell cmdlet which is part of the AppAssure PowerShell Module that's installed on your AppAssure Core server version 5.3.1 or higher allows you to determine what servers are being protected by a particular AppAssure Core server.

Since I'm running PowerShell version 3 on the AppAssure Core server shown in the example below, I didn't have to explicitly import the AppAssure PowerShell module, if you're running PowerShell version 2, you would need to run <em>Import-Module</em> <em>AppAssurePowerShellModule</em> prior to running the <em>Get-ProtectedServers</em> cmdlet. The following example shows that you can retrieve some useful information with the <em>Get-ProtectedServers</em> cmdlet by simply running it without any parameters:

<img class="alignnone size-full wp-image-6494" alt="appassure1" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure1.png" width="640" height="439" />

To determine what other properties are available for this cmdlet, just like any other cmdlet that produces output, pipe it to <em>Get-Member</em>. One thing to note is the column headers such as "Display Name" and "Is Paused" as shown in the previous example which have a space between the words are not the actual property names. The property name for each of these items as shown in the following results doesn't contain a space between the words:

<img class="alignnone size-full wp-image-6495" alt="appassure2" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure2.png" width="640" height="530" />

I want to filter the output of the <em>Get-ProtectedServers</em> cmdlet down to a subset so I'll view the full help for this cmdlet to determine if it has any parameters to filter with:

<img class="alignnone size-full wp-image-6496" alt="appassure3" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure3.png" width="640" height="542" />

Based on the results of the full help in the previous image, there are no parameters for filtering left so we'll have to resort to piping our results to the <em>Where-Object</em> cmdlet and perform our filtering with that cmdlet. I only want to return a list of servers that should be protected by this AppAssure Core server where the current status is not equal to protected (show me any servers with a status other than protected):

Note: Each of the following examples use the new PowerShell version 3 simplified <em>Where-Object</em> syntax.

<img class="alignnone size-full wp-image-6497" alt="appassure4" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure4.png" width="640" height="197" />

I've been working on updating these client agents to the latest version of AppAssure so the problem is probably that the AppAssure Agent service is not running on those particular servers. Now I only want to retrieve a list of just the server names where the status is not protected. By using the -expand property with the <em>Select-Object</em> cmdlet, the server names are returned as strings without a column header:

<img class="alignnone size-full wp-image-6498" alt="appassure5" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure5.png" width="640" height="151" />

PowerShell remoting is enabled on all of the servers in this environment. I'm now going to place the command we previously created inside of parentheses to feed the <em>Invoke-Command</em> cmdlet the list of server names that aren't currently protected to determine what the status of the AppAssureAgent service is on them:

<img class="alignnone size-full wp-image-6499" alt="appassure6" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure6.png" width="640" height="219" />

Let's modify the previous command slightly to attempt to start the AppAssureAgent service on each of these servers. I've replaced the <em>Get-Service</em> cmdlet with the <em>Start-Service</em> cmdlet. I've also added the -Passthru parameter since the <em>Start-Service</em> cmdlet doesn't return any results by default and I want to see what the results are without having to run another PowerShell command:

<img class="alignnone size-full wp-image-6500" alt="appassure7" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure7.png" width="640" height="381" />

Now that all of the agents are protected, I want to see which ones are still in need of updating. I'll sort the results so the servers with the out of date agents are listed first:

<img class="alignnone size-full wp-image-6501" alt="appassure8" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure8.png" width="640" height="427" />

Let's refine that list a little more and only return a list where the version is not equal to the latest version. The sorting isn't really necessary in this scenario, although if more than one previous version of the AppAssure agent was on different servers, it would have mattered:

<img class="alignnone size-full wp-image-6502" alt="appassure9" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure9.png" width="640" height="162" />

Looks like I have three more servers to update the AppAssure agent on and all of them will be updated, then I can move on to doing more fun stuff with PowerShell.

µ