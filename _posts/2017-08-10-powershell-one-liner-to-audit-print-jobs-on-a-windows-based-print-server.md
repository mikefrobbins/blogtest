---
ID: 15504
post_title: >
  PowerShell One-Liner to Audit Print Jobs
  on a Windows based Print Server
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/08/10/powershell-one-liner-to-audit-print-jobs-on-a-windows-based-print-server/
published: true
post_date: 2017-08-10 07:30:58
---
You've been tasked with auditing print jobs on your company's Windows based print server to determine who is wasting so much paper, toner, and causing excessive wear and tear on printers. No budget exists for this task and you need to have something to show by the end of the day.

You would probably start off by searching the Internet, but most of the results you'll find to accomplish this task are over-complicated or simply don't work. Luckily this task can be accomplished with a PowerShell one-liner. The event log that contains the information you're looking for is "Microsoft-Windows-PrintService/Operational". It's disabled by default and needs to be enabled before it can start logging the information that you'll want to query.

<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/printing-history3a.jpg"><img class="alignnone size-full wp-image-15515" src="http://mikefrobbins.com/wp-content/uploads/2017/08/printing-history3a.jpg" alt="" width="373" height="387" /></a>

First, check the status of the event log to confirm that it's not already enabled. This method of enabling the event log won't work with classic event logs so if you're enabling or disabling a different event log, confirm that it's not one of those. In addition to returning results, the first command stores the results in a variable named "PrinterLog". Use that variable to enable the log and then save the changes. Then check to see if the changes did indeed take effect:
<pre class="lang:ps decode:true">Get-WinEvent -ListLog Microsoft-Windows-PrintService/Operational -OutVariable PrinterLog |
Select-Object -Property LogName, IsClassicLog, IsEnabled

$PrinterLog.set_IsEnabled($true)

$PrinterLog.SaveChanges()

Get-WinEvent -ListLog Microsoft-Windows-PrintService/Operational -OutVariable PrinterLog |
Select-Object -Property LogName, IsClassicLog, IsEnabled</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/printing-history1a.jpg"><img class="alignnone size-full wp-image-15505" src="http://mikefrobbins.com/wp-content/uploads/2017/08/printing-history1a.jpg" alt="" width="859" height="260" /></a>

The printing history can only be retrieved from the point the log was enabled and forward as shown in the previous example.

Simple query that event log for event id 307 using the <em>Get-WinEvent</em> cmdlet. Much of the information that you'll want is stored in the properties collection. Many of the examples I saw on the Internet parse XML for these results and there's simply no reason to add that level of complexity. My chapter (chapter 6) in the <a href="https://www.manning.com/books/powershell-deep-dives" target="_blank" rel="noopener">PowerShell Deep Dives book</a> talks about querying event logs in more detail if that's something you're interested in.

The following example retrieves the information for the past day. Also keep in mind that this event log overwrites by default so the information is not kept forever.
<pre class="lang:ps decode:true ">Get-WinEvent -FilterHashTable @{LogName="Microsoft-Windows-PrintService/Operational"; ID=307; StartTime=(Get-Date).AddDays(-1)} |
Format-Table -Property TimeCreated,
                        @{label='UserName';expression={$_.properties[2].value}},
                        @{label='ComputerName';expression={$_.properties[3].value}},
                        @{label='PrinterName';expression={$_.properties[4].value}},
                        @{label='PrintSize';expression={$_.properties[6].value}},
                        @{label='Pages';expression={$_.properties[7].value}}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/printing-history2a.jpg"><img class="alignnone size-full wp-image-15507" src="http://mikefrobbins.com/wp-content/uploads/2017/08/printing-history2a.jpg" alt="" width="859" height="260" /></a>

You could also output the results to a CSV if needed:
<pre class="lang:ps decode:true">Get-WinEvent -FilterHashTable @{LogName="Microsoft-Windows-PrintService/Operational"; ID=307; StartTime=(Get-Date -OutVariable Now).AddDays(-1)} |
Select-Object -Property TimeCreated,
                        @{label='UserName';expression={$_.properties[2].value}},
                        @{label='ComputerName';expression={$_.properties[3].value}},
                        @{label='PrinterName';expression={$_.properties[4].value}},
                        @{label='PrintSize';expression={$_.properties[6].value}},
                        @{label='Pages';expression={$_.properties[7].value}} |
Export-Csv -Path "Printing Audit - $($($Now).ToString('yyyy-MM-dd')).csv" -NoTypeInformation</pre>
For simplicity, the examples shown in this blog article have been run directly on the print server itself. They could just as easily be run remotely and while <em>Get-WinEvent</em> does have a <em>ComputerName</em> parameter, running it in that manner will more than likely be blocked by the firewall on the remote server. The simplest way to run it remotely would be to wrap the commands shown in this blog article inside of <em>Invoke-Command</em> as long as PowerShell remoting is enabled on the remote server.

µ