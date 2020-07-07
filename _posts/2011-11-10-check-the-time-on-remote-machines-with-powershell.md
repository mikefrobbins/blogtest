---
ID: 3049
post_title: >
  Check the Time on Remote Machines with
  PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/11/10/check-the-time-on-remote-machines-with-powershell/
published: true
post_date: 2011-11-10 07:30:33
---
I ran into an issue lately where the domain controller that hosts the PDC emulator FSMO role for the forest root domain became unavailable and the time for several machines in the domain was far enough off to start causing kerberos related security problems.

Here's a simply Powershell script to query the time on remote machines via WMI. You could use Invoke-Command with Get-Date, but that takes too long compared to just using WMI. I chose to hard code the names I wanted to query directly into the script to eliminate having to keep up with a separate text file or trying to query Active Directory for the names since that may not be possible if the time is way off. This script is my solution to the "I have a problem and I want to eliminate the time on remote machines as a source of it". I want the time and I want it now!
<pre class="lang:ps decode:true">$servers = 'server1', 'server2', 'server3', 'server4', 'server5', 'server6'

ForEach ($server in $servers) {
    $time = ([WMI]'').ConvertToDateTime((gwmi win32_operatingsystem -computername $server).LocalDateTime)
    $server + '  ' + $time
}</pre>
µ