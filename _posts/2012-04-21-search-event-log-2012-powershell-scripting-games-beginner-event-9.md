---
ID: 3807
post_title: 'Search Event Log – 2012 PowerShell Scripting Games Beginner Event #9'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/04/21/search-event-log-2012-powershell-scripting-games-beginner-event-9/
published: true
post_date: 2012-04-21 07:30:13
---
The details of the event scenario and the design points for Beginner Event #9 of the 2012 PowerShell Scripting Games can be found on the “<a href="http://blogs.technet.com/b/heyscriptingguy/archive/2012/04/12/the-2012-scripting-games-beginner-event-9-search-the-event-log.aspx" target="_blank">Hey, Scripting Guys! Blog</a>”.

Find Veto Shutdown Events in the Application Event Log. A screenshot was provided that contains EventID 10001 and Winsrv as the source. Write a one liner to display the date of occurrence and the application name. Your command should be efficient. Complexity will cost you points.

As noted in the comments section of this scenario, you can generate one of these events by opening notepad and then attempting a shutdown. Click cancel to save the document and then cancel on the force shutdown message. The one thing the comment didn't state is that you must type something into notepad and it must be unsaved, that's how you'll end up with these prompts otherwise your machine will just shutdown without any prompts.

This one was fairly tricky and simple at the same time. I went back and forth between using <em>Get-EventLog</em> and <em>Get-WinEvent</em>, but decided that <em>Get-EventLog</em> would be less complex which was one of the design points.

Here's one of the <em>Get-WinEvent</em> commands I worked on. The <em>-FilterXPath</em> parameter seems to be something that there isn't much documentation on. The only decent documentation I found on this parameter was in Chapter 23 of Lee Holmes's (<a href="http://twitter.com/Lee_Holmes" target="_blank">@Lee_Holmes</a>) "<a href="http://shop.oreilly.com/product/9780596801519.do" target="_blank">Windows PowerShell Cookbook, Second Edition</a>" book.
<pre class="lang:ps decode:true">Get-Winevent -ProviderName Microsoft-Windows-Winsrv -FilterXPath "*[System[(EventID=10001)]]" |
Select TimeCreated, @{l='AppName, ResponseTime';e={$_.Properties[0].Value, $_.Properties[1].Value}}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be9-11.png"><img class="alignnone size-full wp-image-3900" title="2012sg-be9-11" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be9-11.png" width="640" height="91" /></a>

Once I figured out what the full name of the "Source" was and that only the veto events generated InstanceID 10001 in that particular source, this one wasn't too difficult. Here's the script I submitted:
<pre class="lang:ps decode:true crayon-selected" title="Beginner Category Event #9 of the 2012 Scripting Games">Get-EventLog -LogName Application -Source Microsoft-Windows-Winsrv -InstanceId 10001 |
Format-Table TimeGenerated, ReplacementStrings</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be9-21.png"><img class="alignnone size-full wp-image-3902" title="2012sg-be9-21" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be9-21.png" width="640" height="92" /></a>

In hindsight, I should have piped to <em>Select-Object</em> instead of <em>Format-Table</em> since the results would have been the same and it's always preferable to return an object. Boe Prox (<a href="http://twitter.com/proxb" target="_blank">@proxb</a>) wrote a blog: "<a href="http://learn-powershell.net/2012/04/18/scripting-games-2012-know-when-to-use-format-table/" target="_blank">Scripting Games 2012: Know When To Use Format-Table</a>" that discusses this subject in detail. His blog teaches you why to Filter | Select | Sort.

This screenshot shows you how much more efficient the <em>Get-WinEvent</em> command (.177 seconds) is than the <em>Get-EventLog</em> command (7.74 seconds) and that's only with two of these events in the log. The difference seems to be even greater with more log entries.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be9-32.png"><img class="alignnone size-full wp-image-3914" title="2012sg-be9-32" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be9-32.png" width="504" height="490" /></a>

View my entry for this event on the <a href="http://2012sg.poshcode.org/5277" target="_blank">PowerShell Code Repository site</a>.

µ