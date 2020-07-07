---
ID: 3674
post_title: 'Working with WMI &#8211; 2012 PowerShell Scripting Games Beginner Event #6'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/04/18/working-with-wmi-2012-powershell-scripting-games-beginner-event-6/
published: true
post_date: 2012-04-18 07:30:49
---
The details of the event scenario and the design points for Beginner Event #6 of the 2012 PowerShell Scripting Games can be found on the “<a href="http://blogs.technet.com/b/heyscriptingguy/archive/2012/04/09/2012-scripting-games-beginner-event-6-compute-uptime-for-local-computer.aspx" target="_blank">Hey, Scripting Guys! Blog</a>”.

Write a PowerShell script to determine the uptime of servers by using the WMI class WMI32_OperatingSystem. The script should display the server name, how many days, hours, and minutes the server has been up.

As usual, I started out by running <em>Get-Help Get-WMIObject</em> to determine what the available parameters were for this cmdlet. I ran <em>Get-WMIObject -Class Win32_OperatingSystem</em> but that didn't return much:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be6-1.png"><img class="alignnone size-full wp-image-3774" title="2012sg-be6-1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be6-1.png" width="371" height="131" /></a>

I could pipe this to <em>Get-Member</em> to determine what the property names are, but sometimes it's easier to pipe it to <em>Format-List *</em> to not only see what the available properties are, but to also see what their values are. I've selected only the properties that are inportant as shown in the screenshot below since the results of all (*) are lengthy.
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be6-2.png"><img class="alignnone size-full wp-image-3775" title="2012sg-be6-2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be6-2.png" width="433" height="118" /></a>

I did some research online and determined that I would need to convert the LastBootUpTime to a DateTime to be able to use it. I then started out using <em>Get-Date</em> for the current time and <em>$Env:ComputerName</em> for the computer name. Then I thought about the requirements of using WMI. Based on the information in the screenshot above, I could pull everything I needed out of that single WMI class. I could use LocalDateTime instead of using <em>Get-Date</em> and CSName instead of <em>$Env:ComputerName</em>. I read somewhere that it's better to use CSName than __Server for the computer name although they both contained the same value.

My research on how to convert the DateTime information returned by WMI to a useable format lead me to the "<a href="http://blogs.technet.com/b/heyscriptingguy/archive/2010/08/05/manipulating-dates-from-windows-management-instrumentation-wmi.aspx" target="_blank">Manipulating Dates Returned by Windows Management Instrumentation (WMI)</a>" blog on the Hey, Scripting Guy! Blog.

One of the prep video's by Chris Brown (<a href="http://twitter.com/chrisbrownie" target="_blank">@chrisbrownie</a>) titled "2012 Scripting Games - Video 2 - Working with Dates" on <a href="http://powershelldownunder.com/" target="_blank">PowerShellDownUnder.com</a> helped me figure out how to format my output. I initially defined variables for each of the items such as $Days = $Uptime.Days and the code in my script was eleven lines long, by using $($Uptime.Days) directly in the output, I was able to cut my code to five lines and simplify it.

I tried to match the output in the screenshot provided in the scenario so I wanted the "as of" date to be in AM/PM format instead of what was being returned by default (24 hour format). I found an MSDN article on "<a href="http://msdn.microsoft.com/en-us/library/az4se3k1.aspx" target="_blank">Standard Date and Time Format Strings</a>" which assisted me in converting the date to a "General date/time pattern (long time)" using .ToString('G').

Here's the script I submitted for this event:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be6-31.png"><img class="alignnone size-full wp-image-3783" title="2012sg-be6-31" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be6-31.png" width="640" height="87" /></a>

I’ve formatted it slightly different in the above screenshot so it fits better on this blog.
<pre class="lang:ps decode:true" title="Beginner Category Event #6 of the 2012 Scripting Games">$win32os = Get-WmiObject -Class Win32_OperatingSystem 
$now = ($win32os.ConvertToDateTime($win32os.LocalDateTime))
$lastboot = ($win32os.ConvertToDateTime($win32os.LastBootupTime))
$uptime = $now - $lastboot
Write-Output "The computer $($win32os.csname) has been up for $($uptime.days) days $($uptime.hours) hours $($uptime.minutes) minutes, $($uptime.seconds) seconds as of $($now.tostring('G'))"</pre>
Here’s a link to my entry for this event on the <a href="http://2012sg.poshcode.org/4514" target="_blank">PowerShell Code Repository site</a>.

µ