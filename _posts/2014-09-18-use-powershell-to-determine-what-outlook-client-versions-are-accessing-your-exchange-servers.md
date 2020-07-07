---
ID: 10434
post_title: >
  Use PowerShell to Determine what Outlook
  Client Versions are accessing your
  Exchange Servers
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/09/18/use-powershell-to-determine-what-outlook-client-versions-are-accessing-your-exchange-servers/
published: true
post_date: 2014-09-18 07:30:55
---
Earlier this month I saw a blog article on "<a href="http://www.expta.com/" target="_blank">The EXPTA {blog}</a>" about "<a href="http://www.expta.com/2014/09/reporting-outlook-client-versions-using.html" target="_blank">Reporting Outlook Client Versions Using Log Parser Studio</a>" and I thought I would show you a simple alternative using PowerShell that accomplishes the same task while giving you some additional flexibility.

This simple PowerShell function can be used to parse the Exchange Server RPC logs.
<pre class="lang:ps decode:true " title="Get-MrRCAProtocolLog">#Requires -Version 3.0
function Get-MrRCAProtocolLog {

&lt;#
.SYNOPSIS
    Identifies and reports which Outlook client versions are being used to access Exchange.
 
.DESCRIPTION
    Get-MrRCAProtocolLog is an advanced PowerShell function that parses Exchange Server RPC
    logs to determine what Outlook client versions are being used to access the Exchange Server.
 
.PARAMETER LogFile
    The path to the Exchange RPC log files.
 
.EXAMPLE
     Get-MrRCAProtocolLog -LogFile 'C:\Program Files\Microsoft\Exchange Server\V15\Logging\RPC Client Access\RCA_20140831-1.LOG'

.EXAMPLE
     Get-ChildItem -Path '\\servername\c$\Program Files\Microsoft\Exchange Server\V15\Logging\RPC Client Access\*.log' |
     Get-MrRCAProtocolLog |
     Out-GridView -Title 'Outlook Client Versions'
 
.INPUTS
    String
 
.OUTPUTS
    PSCustomObject
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [Parameter(Mandatory,
                   ValueFromPipeline)]
        [ValidateScript({
            Test-Path -Path $_ -PathType Leaf -Include '*.log'
        })]
        [string[]]$LogFile
    )

    PROCESS {
        foreach ($file in $LogFile) {
            $Headers = (Get-Content -Path $file -TotalCount 5 | Where-Object {$_ -like '#Fields*'}) -replace '#Fields: ' -split ','
                    
            Import-Csv -Header $Headers -Path $file |
            Where-Object operation -eq 'Connect' |
            Select-Object -Unique -Property @{label='User';expression={$_.'client-name' -replace '^.*cn='}},
                                            @{label='DN';expression={$_.'client-name'}},
                                            client-software,
                                            @{label='Version';expression={$_.'client-software-version'}},
                                            client-mode,
                                            client-ip,
                                            protocol
        }
    }
}</pre>
Piping this command to <em>Out-GridView</em> provides the same output in a similar looking interface as what was seen in the blog article mentioned earlier that was linked to.
<pre class="lang:ps decode:true">Get-ChildItem -Path '\\mail01\c$\Program Files\Microsoft\Exchange Server\V15\Logging\RPC Client Access\*.log' |
Get-MrRCAProtocolLog |
Out-GridView -Title 'Outlook Client Versions'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/rcaprotocol-log1.png"><img class="alignnone size-full wp-image-10435" src="http://mikefrobbins.com/wp-content/uploads/2014/09/rcaprotocol-log1.png" alt="rcaprotocol-log1" width="877" height="73" /></a>

While you could accomplish this task as shown in the previous example, you would be pulling all of the log files across the network only to process and sort them down on your computer which may not make your network administrators too happy. A better approach might be to add the function to a module on the Exchange server.

In the following example, we've done just that. The function has been added to a module that exists on the actual Exchange server in a location specified in the $env:PSModulePath and PowerShell version 3 introduced module auto-loading so no need to explicitly import the module. We'll take advantage of PowerShell remoting so the filtering and processing is performed on the Exchange Server itself. Notice that we're only grabbing the log files that have been modified in the last 90 days in this example. Using this method, only the results are transmitted across the network to our workstation. We've also specifed alternate credentials for the command to be run on the Exchange server since being logged into your workstation or even running PowerShell with domain elevated credentials is trouble waiting for a place to happen.
<pre class="lang:ps decode:true">Invoke-Command -ComputerName mail01 {
    Get-ChildItem -Path 'C:\Program Files\Microsoft\Exchange Server\V15\Logging\RPC Client Access\*.log' |
    Where-Object LastWriteTime -gt (Get-Date).AddDays(-90) |
    Get-MrRCAProtocolLog
} -Credential (Get-Credential) |
Out-GridView -Title 'Outlook Client Versions'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/rcaprotocol-log5.png"><img class="alignnone size-full wp-image-10456" src="http://mikefrobbins.com/wp-content/uploads/2014/09/rcaprotocol-log5.png" alt="rcaprotocol-log5" width="878" height="441" /></a>

Here's the type of output that is generated and it's easy enough to sort inside of <em>Out-GridView</em> instead of manually adding that to the PowerShell function itself which would adversely affect the performance since all of the results would have to finish processing before any of them would be displayed.

<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/rcaprotocol-log2b.png"><img class="alignnone size-full wp-image-10444" src="http://mikefrobbins.com/wp-content/uploads/2014/09/rcaprotocol-log2b.png" alt="rcaprotocol-log2b" width="931" height="759" /></a>

While that's nice and all, why stop there when we have the Power of PowerShell (with a capital "P" no less) at our fingertips?

Not sure about you, but 15.0.4615.1000 and 12.0.6672.5000 doesn't mean much to me other than being a manual process to search TechNet to determine what versions of Outlook those are for each and every version, each time we run this. If you're going to do something more than once, do it in PowerShell and be done with it &lt;period&gt;. What do I mean? Look up the build number to version translation once and write another reusable PowerShell function to automatically perform the translation for us from now on.

A simple helper function that translates the Outlook build number to the actual version has been created and minor modifications have been made to the original function as shown below. I've separated the function to translate the build number to the version to also make it into a reusable function in case I ever want to use it with another process.
<pre class="lang:ps decode:true " title="Get-MrRCAProtocolLog &amp; Get-MrOutlookVersion">#Requires -Version 3.0
function Get-MrRCAProtocolLog {

&lt;#
.SYNOPSIS
    Identifies and reports which Outlook client versions are being used to access Exchange.
 
.DESCRIPTION
    Get-MrRCAProtocolLog is an advanced PowerShell function that parses Exchange Server RPC
    logs to determine what Outlook client versions are being used to access the Exchange Server.
 
.PARAMETER LogFile
    The path to the Exchange RPC log files.
 
.EXAMPLE
     Get-MrRCAProtocolLog -LogFile 'C:\Program Files\Microsoft\Exchange Server\V15\Logging\RPC Client Access\RCA_20140831-1.LOG'

.EXAMPLE
     Get-ChildItem -Path '\\servername\c$\Program Files\Microsoft\Exchange Server\V15\Logging\RPC Client Access\*.log' |
     Get-MrRCAProtocolLog |
     Out-GridView -Title 'Outlook Client Versions'
 
.INPUTS
    String
 
.OUTPUTS
    PSCustomObject
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [Parameter(Mandatory,
                   ValueFromPipeline)]
        [ValidateScript({
            Test-Path -Path $_ -PathType Leaf -Include '*.log'
        })]
        [string[]]$LogFile
    )

    PROCESS {
        foreach ($file in $LogFile) {
            $Headers = (Get-Content -Path $file -TotalCount 5 | Where-Object {$_ -like '#Fields*'}) -replace '#Fields: ' -split ','
                    
            Import-Csv -Header $Headers -Path $file |
            Where-Object {$_.operation -eq 'Connect' -and $_.'client-software' -eq 'outlook.exe'} |
            Select-Object -Unique -Property @{label='User';expression={$_.'client-name' -replace '^.*cn='}},
                                            @{label='DN';expression={$_.'client-name'}},
                                            client-software,
                                            @{label='Version';expression={Get-MrOutlookVersion -OutlookBuild $_.'client-software-version'}},
                                            client-mode,
                                            client-ip,
                                            protocol
        }
    }
}

function Get-MrOutlookVersion {
    param (
        [string]$OutlookBuild
    )
    switch ($OutlookBuild) {                
        {$_ -ge '15.0.4569.1506'} {'Outlook 2013 SP1'; break}
        {$_ -ge '15.0.4420.1017'} {'Outlook 2013 RTM'; break}
        {$_ -ge '14.0.7015.1000'} {'Outlook 2010 SP2'; break}
        {$_ -ge '14.0.6029.1000'} {'Outlook 2010 SP1'; break}
        {$_ -ge '14.0.4763.1000'} {'Outlook 2010 RTM'; break}
        {$_ -ge '12.0.6607.1000'} {'Outlook 2007 SP3'; break}
        {$_ -ge '12.0.6423.1000'} {'Outlook 2007 SP2'; break}
        {$_ -ge '12.0.6212.1000'} {'Outlook 2007 SP1'; break}
        {$_ -ge '12.0.4518.1014'} {'Outlook 2007 RTM'; break}
        Default {'Unknown'}
    }
}</pre>
Now we have a human readable version of Outlook displayed in the results.

<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/rcaprotocol-log3.png"><img class="alignnone size-full wp-image-10440" src="http://mikefrobbins.com/wp-content/uploads/2014/09/rcaprotocol-log3.png" alt="rcaprotocol-log3" width="909" height="759" /></a>

Based on those results, I have users using unpatched versions of Outlook 2007 and 2013. Sounds like it's time to enter a help desk ticket for the desktop group to get that corrected.

The nice thing about writing a reusable function like this is it's multipurpose so I can use it not only with <em>Out-GridView</em>, but since its output produces objects, the results can be sent to a text, CSV, or HTML file, an email message, or worked with further such as to uniquely return a list of all Outlook client versions that have accessed this Exchange Server.
<pre class="lang:ps decode:true  ">Get-ChildItem -Path '\\mail01\c$\Program Files\Microsoft\Exchange Server\V15\Logging\RPC Client Access\*.log' |
Get-MrRCAProtocolLog |
Select-Object -Property Version -Unique |
Sort-Object -Property Version -Descending</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/rcaprotocol-log4.png"><img class="alignnone size-full wp-image-10442" src="http://mikefrobbins.com/wp-content/uploads/2014/09/rcaprotocol-log4.png" alt="rcaprotocol-log4" width="877" height="203" /></a>

How long did it take to create these functions? Not long as I had previously done something similar with IIS log files.

µ