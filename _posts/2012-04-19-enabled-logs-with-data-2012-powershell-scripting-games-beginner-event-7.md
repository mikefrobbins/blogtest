---
ID: 3787
post_title: 'Enabled Logs with Data – 2012 PowerShell Scripting Games Beginner Event #7'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/04/19/enabled-logs-with-data-2012-powershell-scripting-games-beginner-event-7/
published: true
post_date: 2012-04-19 07:30:57
---
The details of the event scenario and the design points for Beginner Event #7 of the 2012 PowerShell Scripting Games can be found on the “<a href="http://blogs.technet.com/b/heyscriptingguy/archive/2012/04/10/the-2012-scripting-games-beginner-event-7-display-a-list-of-enabled-logs.aspx" target="_blank">Hey, Scripting Guys! Blog</a>”.

<span style="color: #000000;">Display a list of enabled logs that contain data. Do not display errors. Include hidden logs. Display the complete log name and number of entries. Sort by the logs with the most entries in them.</span>

My research on this one lead me to the "<a href="http://blogs.technet.com/b/heyscriptingguy/archive/2011/03/07/use-powershell-to-query-all-event-logs-for-recent-events.aspx" target="_blank">Use PowerShell to Query All Event Logs for Recent Events</a>" blog article on the Hey, Scripting Guy! Blog. I also used the online help by running <a href="http://technet.microsoft.com/en-us/library/hh849682.aspx" target="_blank"><em>Get-Help Get-WinEvent -Online</em></a>. Example #3 was of particular interest:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be7-1.png"><img class="alignnone size-full wp-image-3790" title="2012sg-be7-1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be7-1.png" width="640" height="182" /></a>

By viewing the help, I was able to determine that the <em>-Force</em> parameter would display debug and analytic logs that are hidden by default. I used <em>-ErrorAction SilentlyContinue</em> since I wasn't required to handle errors, only to prevent them from displaying. Then I piped my command to the <em>Where-Object</em> cmdlet similar to the help for example #3 as shown in the screenshot above. I added a <em>-and</em> and the other condition of <em>IsEnabled</em>. I determined that <em>IsEnabled</em> was a property by piping the first command to <em>Get-Member:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be7-3.png"><img class="alignnone size-full wp-image-3795" title="2012sg-be7-3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be7-3.png" width="582" height="313" /></a> </em>

<span style="color: #000000;">Then I just piped it to <em>Sort-Object RecordCount -Descending</em> to sort as required. The most difficult portion of this entire scenario was the requirement of "You should display the complete log name, and the number of entries in the log." I initially piped my output to <em>Format-Table</em> with the <em>-AutoSize</em> parameter. I tested my code thoroughly and determined that if I used the <em>-AutoSize</em> parameter, this requirement would not be met if my PowerShell console window was narrow enough such as 60 pixels wide:</span>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be7-4.png"><img class="alignnone size-full wp-image-3834" title="2012sg-be7-4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be7-4.png" width="386" height="474" /></a>

<span style="color: #000000;">The <em>-AutoSize</em> parameter does not guarantee that the data will not be truncated as you can see here in this PowerShell console window that had been re-sized to 60 pixels wide:</span>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/ps-vii-auto.png"><img class="alignnone size-full wp-image-3750" title="ps-vii-auto" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps-vii-auto.png" width="577" height="742" /></a>

<span style="color: #000000;">I decided to use the <em>-Wrap</em> parameter since the width of the judges PowerShell console window was outside of my control and it would ensure that this requirement would be met no matter how narrow the PowerShell console was as shown in this 60 pixel wide window:</span>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/ps-vii-wrap.png"><img class="alignnone size-full wp-image-3751" title="ps-vii-wrap" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps-vii-wrap.png" width="577" height="742" /></a>

<em>-Wrap</em> at 15 pixels wide:            <em>-AutoSize</em> at 15 pixels wide:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/ps-vii-wrap15.png"><img class="alignnone size-full wp-image-3846" title="ps-vii-wrap15" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps-vii-wrap15.png" width="400" height="742" /></a>

Now which one of these options is guaranteed to meet the requirements of this scenario?

Here's the one liner I submitted for this event:
<pre class="lang:ps decode:true " title="Beginner Category Event #7 of the 2012 Scripting Games">Get-WinEvent -ListLog * -Force -ErrorAction SilentlyContinue | Where-Object {$_.RecordCount -and $_.IsEnabled} | Sort-Object RecordCount -Descending | Format-Table -Property LogName, RecordCount -Wrap</pre>
µ