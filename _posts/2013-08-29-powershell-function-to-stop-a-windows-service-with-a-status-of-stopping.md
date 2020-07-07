---
ID: 8176
post_title: >
  PowerShell Function to Stop a Windows
  Service with a Status of Stopping
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/08/29/powershell-function-to-stop-a-windows-service-with-a-status-of-stopping/
published: true
post_date: 2013-08-29 07:30:32
---
If you're reading this blog article, you've probably tried to stop a Windows Service and it's hung or stuck with a status of "Stopping":

<a href="http://mikefrobbins.com/wp-content/uploads/2013/08/pendingservice1.png"><img class="alignnone size-full wp-image-8179" src="http://mikefrobbins.com/wp-content/uploads/2013/08/pendingservice1.png" alt="pendingservice1" width="497" height="153" /></a>

An average Windows Admin would say "The server is going to have to be rebooted to clear up this problem". This problem can be resolved with PowerShell though without the need for a restart.

The machine I'm testing these examples on just happens to be running PowerShell version 2 so I'll use the v2 syntax to see if the <em>Get-Service</em> cmdlet has any properties that may help. I want to see values with the property names so I'll pipe the results to <em>Format-List</em> * <em>-Force</em> as shown in the following example:
<pre class="lang:ps decode:true">Get-Service |
Where-Object {$_.Status -eq 'StopPending'} |
Format-List * -Force</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/08/pendingservice2.png"><img class="alignnone size-full wp-image-8180" src="http://mikefrobbins.com/wp-content/uploads/2013/08/pendingservice2.png" alt="pendingservice2" width="467" height="325" /></a>

There doesn't appear to be any properties that can help so I'll pipe <em>Get-Service</em> to <em>Get-Member</em> to make sure there aren't any methods that could help:
<pre class="lang:ps decode:true">Get-Service | Get-Member</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/08/pendingservice3.png"><img class="alignnone size-full wp-image-8181" src="http://mikefrobbins.com/wp-content/uploads/2013/08/pendingservice3.png" alt="pendingservice3" width="665" height="830" /></a>

I don't see anything in those results either that could help, but maybe we can just pipe the service with the issue to the <em>Stop-Process</em> cmdlet and use the <em>Force</em> parameter to stop it:
<pre class="lang:ps decode:true">Get-Service | Get-Member
Where-Object {$_.Status -eq 'StopPending'} |
Stop-Service -Force</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/08/pendingservice4.png"><img class="alignnone size-full wp-image-8182" src="http://mikefrobbins.com/wp-content/uploads/2013/08/pendingservice4.png" alt="pendingservice4" width="668" height="207" /></a>

No such luck. It's looking like that restart may not be such a bad idea, but let's give WMI a try before we resort to that option. We're prototyping at this point so we're not going to worry about filtering to the left:
<pre class="lang:ps decode:true">Get-WmiObject -Class win32_service |
Where-Object {$_.state -eq 'stop pending'}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/08/pendingservice5a.png"><img class="alignnone size-full wp-image-8185" src="http://mikefrobbins.com/wp-content/uploads/2013/08/pendingservice5a.png" alt="pendingservice5a" width="497" height="207" /></a>

The "ProcessId" property looks promising.

I'm sure most of you have either read about or seen examples on the Internet where someone tries to pipe <em>Get-Service</em> to <em>Stop-Process</em> and maybe they're lucky enough to stop a process or two because those couple of service names just happen to match up to the process names, but generally speaking, service names and process names don't match up.

We're going to use a modified version of that same sort of example here, but we're going to use the value of a service's "ProcessId" which we'll retrieve from WMI and feed that information to <em>Stop-Process</em>. While we're at it, we'll just write a simple function to accomplish this task and apply all of those best practices such as filtering to the left:
<pre class="lang:ps decode:true " title="Stop-PendingService">function Stop-PendingService {

&lt;#
.SYNOPSIS
    Stops one or more services that is in a state of 'stop pending'.
 
.DESCRIPTION
     Stop-PendingService is a function that is designed to stop any service
     that is hung in the 'stop pending' state. This is accomplished by forcibly
     stopping the hung services underlying process.
 
.EXAMPLE
     Stop-PendingService
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    $Services = Get-WmiObject -Class win32_service -Filter "state = 'stop pending'"
    if ($Services) {
        foreach ($service in $Services) {
            try {
                Stop-Process -Id $service.processid -Force -PassThru -ErrorAction Stop
            }
            catch {
                Write-Warning -Message "Unexpected Error. Error details: $_.Exception.Message"
            }
        }
    }
    else {
        Write-Output "There are currently no services with a status of 'Stopping'."
    }
}</pre>
Our <em>Stop-PendingService</em> function will stop any service that has a state of "Stop Pending" on the local computer:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/08/pendingservice6.png"><img class="alignnone size-full wp-image-8186" src="http://mikefrobbins.com/wp-content/uploads/2013/08/pendingservice6.png" alt="pendingservice6" width="668" height="123" /></a>

Instead of writing a bunch of code in this function to connect to a remote machine, I'll just leverage the power of PowerShell remoting via the <em>Invoke-Command</em> cmdlet and have it run this function that only exists on my local machine against the remote machine that has the service "Stopping" issue:
<pre class="lang:ps decode:true">. .\Stop-PendingService.ps1
Invoke-Command -ComputerName Server1 -ScriptBlock ${Function:\Stop-PendingService} -Credential (Get-Credential)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/08/pendingservice7.png"><img class="alignnone size-full wp-image-8187" src="http://mikefrobbins.com/wp-content/uploads/2013/08/pendingservice7.png" alt="pendingservice7" width="597" height="215" /></a>

The <em>Stop-PendingService</em> function can be downloaded from the <a href="http://gallery.technet.microsoft.com/scriptcenter/PowerShell-Function-to-405cfd49" target="_blank">TechNet Script Repository</a>.

If you found this interesting and want to hear more of my PowerShell ramblings, I'm speaking on "Using CIM Cmdlets and CIM Sessions" at the Philadelphia PowerShell User Group meeting on Thursday, September 5th at 6pm EDT. Sign up <a href="http://powershell.org/wp/2013/08/11/philadelphia-meeting-september-5th-2013/" target="_blank">here</a> to attend in person or virtually via Microsoft Lync.

µ