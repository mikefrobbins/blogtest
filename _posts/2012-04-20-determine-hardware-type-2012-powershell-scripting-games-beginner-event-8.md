---
ID: 3805
post_title: 'Determine Hardware Type – 2012 PowerShell Scripting Games Beginner Event #8'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/04/20/determine-hardware-type-2012-powershell-scripting-games-beginner-event-8/
published: true
post_date: 2012-04-20 07:30:20
---
The details of the event scenario and the design points for Beginner Event #8 of the 2012 PowerShell Scripting Games can be found on the “<a href="http://blogs.technet.com/b/heyscriptingguy/archive/2012/04/11/the-2012-scripting-games-beginner-event-8-is-computer-desktop-or-laptop.aspx" target="_blank">Hey, Scripting Guys! Blog</a>”.

Write a script to determine if a computer is a laptop or a desktop from a hardware prospective and display the output on the console. If this requires admin rights, you should detect if it is running as an admin or standard user. Extra points for writing a simple function that returns a boolean.

I kind of figured this was going to be a WMI thing since it involved hardware. Some research turned up an <a href="http://msdn.microsoft.com/en-us/library/windows/desktop/aa394102(v=vs.85).aspx" target="_blank">MSDN article</a> on the Win32_ComputerSystem class which contained a PCSystemType property. You could also search through the WMI classes using <em>Get-WMIObject -List *computer*.</em>

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be8-1.png"><img class="alignnone size-full wp-image-3825" title="2012sg-be8-1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be8-1.png" width="550" height="287" /></a>

Based on that MSDN article, only a value of 2 is a mobile computer (laptop). Based on some of the comments listed on the scenario requirements for this event, it appears to be safe to assume that all others are considered to be desktops.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be8-2.png"><img class="alignnone size-full wp-image-3826" title="2012sg-be8-2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be8-2.png" width="311" height="584" /></a>

I broke out my "<a href="http://www.manning.com/jones/" target="_blank">Learn Windows PowerShell in a Month of Lunches</a>" book and headed to chapter 19 to figure out how to write a simple function. Section 19.5 - Returning objects from a function was close enough to figure it out. I used If/Else and designed it so Else would never have to run unless the value was equal to 2.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be8-3.png"><img class="alignnone size-full wp-image-3827" title="2012sg-be8-3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be8-3.png" width="613" height="281" /></a>

Then all I had to do is call the function and display the output based on whether or not the function returned true or false with another If/Else statement. Here's a link to my script on the <a href="http://2012sg.poshcode.org/5263" target="_blank">PowerShell Code Repository</a> site. The comments are interesting.
<pre class="lang:ps decode:true crayon-selected" title="Beginner Category Event #8 of the 2012 Scripting Games">function Get-HardwareType {

&lt;#

.SYNOPSIS
Get-HardwareType is used to determine if a computer is a laptop of desktop.

.DESCRIPTION
Get-HardwareType is used to determine a computer's hardware type of whether or not the
computer is a laptop or a desktop.

#&gt;

    $hardwaretype = Get-WmiObject -Class Win32_ComputerSystem -Property PCSystemType
        If ($hardwaretype -ne 2)
        {
        return $true
        }
        Else
        {
        return $false
        }}

If (Get-HardwareType)
{
"$Env:ComputerName is a Desktop"
}
Else
{
"$Env:ComputerName is a Laptop"
}</pre>
<span style="text-decoration: underline;">Update:
</span>After I wrote this blog, but before it was published I received some peer feedback on this event (which I appreciate). Based on this feedback, it appears that I'm  returning the entire object instead of the single element (PCSystemType) as shown here:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be7-41.png"><img class="alignnone size-full wp-image-3863" title="2012sg-be7-4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be7-41.png" width="524" height="199" /></a>

This makes the function always return true.

The code in my script could have been written like this by expanding the property:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be7-5.png"><img class="alignnone size-full wp-image-3864" title="2012sg-be7-5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be7-5.png" width="640" height="26" /></a>

Here's the complete script using this method (<strong>option #1</strong>):
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be7-7.png"><img class="alignnone size-full wp-image-3866" title="2012sg-be7-7" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be7-7.png" width="457" height="294" /></a>

Or by using the dotted notation (<strong>option #2</strong>):
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be7-6.png"><img class="alignnone size-full wp-image-3865" title="2012sg-be7-6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be7-6.png" width="467" height="30" /></a>

Here's the complete script using that method:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be7-9.png"><img class="alignnone size-full wp-image-3873" title="2012sg-be7-9" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be7-9.png" width="554" height="281" /></a>

Or like this using the dotted notation in another way (<strong>option #3</strong>):
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be7-8.png"><img class="alignnone size-full wp-image-3867" title="2012sg-be7-8" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be7-8.png" width="447" height="281" /></a>

Now to solicit some feedback. Does anyone know which of these methods are preferred? Is one better than the other? Is there a best practice when it comes to this sort of thing?

I'm glad this error was caught since I didn't realize there was an issue with the script I submitted. This allows me to learn from it and write better code in the future because the scripting games aren't about winning or losing, they're about learning. I want to remind the judges that this is why it's so important to leave feedback. A three star rating with no comments means absolutely nothing! I led the beginner leaderboard on <a href="http://blogs.technet.com/b/heyscriptingguy/archive/2012/04/13/beginner-leaderboard-of-the-2012-scripting-games-after-5-days.aspx" target="_blank">Day 5</a>, <a href="http://blogs.technet.com/b/heyscriptingguy/archive/2012/04/16/beginner-leaderboard-of-the-2012-scripting-games-after-6-days.aspx" target="_blank">Day 6</a>, <a href="http://blogs.technet.com/b/heyscriptingguy/archive/2012/04/17/beginner-leaderboard-of-the-2012-scripting-games-after-7-days.aspx" target="_blank">Day 7</a>, and <a href="http://blogs.technet.com/b/heyscriptingguy/archive/2012/04/18/beginner-leaderboard-of-the-2012-scripting-games-after-8-days.aspx" target="_blank">Day 8</a>, but winning didn't mean much to me since I already have an all expense paid trip to Microsoft Tech-Ed. This will be the third year in a row I've attended. That just leaves the bragging rights and bragging about being a beginner winner doesn't mean much to someone whose been working as an IT professional for eighteen years. Next year I'll be competing in the advanced competition and I think anyone who finishes in the top 10 of the beginner events should not be allowed to compete as a beginner, but should be required to compete in advanced if they chose to compete.

µ