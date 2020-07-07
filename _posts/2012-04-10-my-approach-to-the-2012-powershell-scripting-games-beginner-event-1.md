---
ID: 3650
post_title: 'My Approach to the 2012 PowerShell Scripting Games Beginner Event #1'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/04/10/my-approach-to-the-2012-powershell-scripting-games-beginner-event-1/
published: true
post_date: 2012-04-10 07:30:40
---
The details of the event scenario and the design points for Beginner Event #1 of the 2012 PowerShell Scripting Games can be found on the “<a href="http://blogs.technet.com/b/heyscriptingguy/archive/2012/04/02/the-2012-scripting-games-beginner-event-1-use-windows-powershell-to-identify-a-working-set-of-processes.aspx" target="_blank">Hey, Scripting Guys! Blog</a>”.

<span style="text-decoration: underline;"><strong>Listed below are my notes about the requirements and design points:</strong>
</span>Computers run Windows 7 and servers run Windows 2008 R2. They’re all in one domain.
PowerShell remoting is enabled on all computers (both servers and desktops).
Use PowerShell to retrieve the top ten processes based on the memory working set.
There’s no need to write a script. The command can be a one liner. You can use aliases.
The command must be capable of running remotely against other domain computers.
Your command should return an object.
You should be able to write the results to a file if required.

I know I'll have to use <em>Get-Process</em> so I started out by just running that cmdlet on my local computer with no parameters to determine what results are returned by default:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be1-1.png"><img class="alignnone size-full wp-image-3651" title="2012sg-be1-1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be1-1.png" width="609" height="139" /></a>

I want the top ten results based off of <em>Working Set</em>. I initially missed this and sorted by Virtual Memory. The screenshot included in the requirement details for event #1 on the Scripting Games site clued me into re-reading the requirements again and catching this. Take your time and read thoroughly. <em>WS(K)</em> is probably the <em>Working Set</em>, but let’s pipe <em>Get-Process</em> to <em>Get-Member</em> to see what the available properties are:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be1-21.png"><img class="alignnone size-full wp-image-3652" title="2012sg-be1-2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be1-21.png" width="549" height="184" /></a>

I can see that <em>WS</em> is an alias to a property named “<em>WorkingSet</em>”. I’ll use the property “<em>WorkingSet</em>” since I’ve always heard if you're providing a command or script to someone else that you shouldn’t use aliases.

In order to get the top ten processes based on <em>WorkingSet</em>, I’ll need to first sort by that property in descending order. Pipe <em>Get-Process</em> to <em>Sort-Object</em> to accomplish that. My first sort was in exactly the wrong order so I ran <em>Get-Help Sort-Object -Full</em> and determined that there was a <em>-Descending</em> parameter that would sort as required.
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be1-3.png"><img class="alignnone size-full wp-image-3653" title="2012sg-be1-3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be1-3.png" width="533" height="116" /></a>

Now that I have it sorted correctly, I can select the first (top) ten. Pipe <em>Sort-Object</em> to <em>Select-Object</em>. Run <em>Get-Help Select-Object -Full</em> to determine what the available parameters are. There's a <em>-First</em> parameter and specifying <em>10</em> meets the requirements.
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be1-4.png"><img class="alignnone size-full wp-image-3654" title="2012sg-be1-4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be1-4.png" width="640" height="108" /></a>

I have what I need as far as the local computer goes. I could simply run it against remote computers using the <em>–ComputerName</em> parameter of <em>Get-Process</em>, but I couldn’t resist using PowerShell remoting since the scenario stated it was enabled on all computers.
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be1-5.png"><img class="alignnone size-full wp-image-3655" title="2012sg-be1-5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be1-5.png" width="640" height="76" /></a>

Using PowerShell remoting also gives me a column that tells me what computer the results are from where using the <em>–ComputerName</em> parameter of <em>Get-Process</em> wouldn’t and the results wouldn’t mean much if it was run against multiple computers. Here's a link to the one liner that I submitted for this event on the <a href="http://2012sg.poshcode.org/2162" target="_blank">PowerShell Code Repository site</a>.
<pre class="lang:ps decode:true " title="Beginner Category Event #1 of the 2012 Scripting Games">Invoke-Command -ComputerName localhost {Get-Process | Sort-Object WorkingSet -Descending | Select-Object -First 10}</pre>
Something that was confusing to me about this particular event was that the command should return an object and another point was writing the results to a file. If you add the code to pipe this to a file doesn't that keep it from outputting an object?

µ