---
ID: 3662
post_title: 'Get-Method | My-Madness | 2012 PowerShell Scripting Games Beginner Event #2'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/04/11/get-method-my-madness-2012-powershell-scripting-games-beginner-event-2/
published: true
post_date: 2012-04-11 07:30:39
---
The details of the event scenario and the design points for Beginner Event #2 of the 2012 PowerShell Scripting Games can be found on the “<a href="http://blogs.technet.com/b/heyscriptingguy/archive/2012/04/03/2012-scripting-games-beginner-event-2-find-stoppable-running-services.aspx" target="_blank">Hey, Scripting Guys! Blog</a>”.

<span style="text-decoration: underline;"><strong>Listed below are my notes about the requirements and design points:</strong>
</span>Find all services that are running and can be stopped.
The command must work against remote computers.
Use the simplest command that will work. You do not need to write a script.
Return the results to the screen, not to a file.
You may use aliases.

I started out by running <em>Get-Service</em> on my local computer:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be2-0.png"><img class="alignnone size-full wp-image-3703" title="2012sg-be2-0" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be2-0.png" width="451" height="214" /></a>

I then ran <em>Get-Help Get-Service -Full</em> to verify that there weren't any parameters I could use to filter with in an attempt to filter as far to the left as possible which would make the command more efficient. I then piped <em>Get-Service</em> to <em>Get-Member</em> to determine what the available properties were. I determined that there's a property named <em>CanStop</em> along with another named <em>Status</em>. I can see one of the status's in the initial command I ran in the screenshot above is "<em>Running</em>", that's one of the pieces to this puzzle.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be2-1.png"><img class="alignnone size-full wp-image-3663" title="2012sg-be2-1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be2-1.png" width="593" height="394" /></a>

If I was unsure of what cmdlet to filter with at this point, I could run <em>Get-Help *filter*</em> to help me locate the cmdlet. I can see <em>Where-Object</em> is in the results:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be2-11.png"><img class="alignnone size-full wp-image-3705" title="2012sg-be2-11" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be2-11.png" width="640" height="103" /></a>

There wasn't a way to filter on the <em>Get-Service</em> cmdlet so that means that I'll need to pipe it to the <em>Where-Object</em> cmdlet. Viewing the full help on this cmdlet shows me an example that's similar to part of what I need. The only difference is it filters on "<em>Stopped</em>" services:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be2-12.png"><img class="alignnone size-full wp-image-3706" title="2012sg-be2-12" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be2-12.png" width="494" height="119" /></a>

Throw a <em>-and</em> into the script block { } of that example and add another property (<em>CanStop</em>) to filter on. In the case of <em>CanStop</em>, there's no need to test it to see if it's is equal to true or false since a boolean defaults to true. Specifying the <em>-ComputerName</em> parameter gives this command a way to be run against remote computers by specifying a remote computer name in place of <em>localhost</em>, an array of computer names in a comma delimited list, or by using (<em>Get-Content C:\filename.txt)</em> to pull the computer names from a text file. You could also pull the computer names from Active Directory by using the <em>Get-ADComputer</em> cmdlet, but that's outside the scope of this scenario.
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be2-2.png"><img class="alignnone size-full wp-image-3664" title="2012sg-be2-2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be2-2.png" width="640" height="150" /></a>
<pre class="lang:ps decode:true" title="Beginner Category Event #2 of the 2012 Scripting Games">Get-Service -ComputerName localhost | Where-Object -FilterScript {$_.Status -eq 'Running' -and $_.CanStop}</pre>
View my entry for this event on the <a href="http://2012sg.poshcode.org/2569" target="_blank">PowerShell Code Repository site</a>.

µ