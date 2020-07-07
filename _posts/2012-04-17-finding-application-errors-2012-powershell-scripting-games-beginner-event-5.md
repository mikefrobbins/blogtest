---
ID: 3672
post_title: 'Finding Application Errors &#8211; 2012 PowerShell Scripting Games Beginner Event 5'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/04/17/finding-application-errors-2012-powershell-scripting-games-beginner-event-5/
published: true
post_date: 2012-04-17 07:30:07
---
The details of the event scenario and the design points for Beginner Event #5 of the 2012 PowerShell Scripting Games can be found on the “<a href="http://blogs.technet.com/b/heyscriptingguy/archive/2012/04/06/2012-scripting-games-beginner-event-5-provide-a-source-and-errors.aspx" target="_blank">Hey, Scripting Guys! Blog</a>”.

Your manager has task you with producing a report of applications that are causing errors on your servers. This report should display the source and number of errors from the application log.

How can I find out what PowerShell cmdlets are available to query the application event log? I could certainly use <em>Get-Help</em>, but I can also use <em>Get-Command</em>:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be5-1.png"><img class="alignnone size-full wp-image-3753" title="2012sg-be5-1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be5-1.png" width="640" height="113" /></a>

After looking at the help topic for these, I chose to use <em>Get-EventLog</em>:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be5-2.png"><img class="alignnone size-full wp-image-3754" title="2012sg-be5-2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be5-2.png" width="640" height="143" /></a>

Based on the available parameters in the screenshot above, I'm going to use <em>Get-EventLog -ComputerName $Env:ComputerName -LogName Application -EventType Error</em> . Specifying the <em>-ComputerName</em> parameter allows it to be run against remote computers. The <em>-LogName</em> parameter is mandatory and you must specify a log name or you'll be prompted for one when the command is run. The <em>-EventType</em> parameter allows you to filter the results down to only errors instead of getting everything only to filter out all the non-errors with Where-Object (<strong>Filter</strong> as far to the <strong>Left</strong> as possible).

Now we need a count of how many times each error shows up in the application log. I searched and found the <em>Group-Object</em> cmdlet. I also took a look at <em>Measure-Object</em>, but <em>Group-Object</em> was a better fit to meet this scenario's objectives. Piping the previous command to <em>Group-Object -Property Source</em> gives it a <em>"Count"</em> column, but also some type of element column named <em>"Group"</em>:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be5-3.png"><img class="alignnone size-full wp-image-3758" title="2012sg-be5-3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be5-3.png" width="640" height="120" /></a>

Displaying help for the <em>Group-Object</em> cmdlet shows it has a <em>-NoElement</em> parameter that will remove this column from the results:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be5-4.png"><img class="alignnone size-full wp-image-3760" title="2012sg-be5-4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be5-4.png" width="369" height="143" /></a>

The other way to find what parameters are available for a cmdlet is to type a space and then minus (dash) after the cmdlet name and then start pressing the tab key to cycle through the available parameters. This uses the tabbed expansion feature to your benefit just like not having to type full cmdlet names or not having to type them in the proper case. I can type <em>get-h</em> and then press tab to have it automatically change to <em>Get-Help</em>.

For additional points, I need to sort by the application with the most errors. Piping the previous command to <em>Get-Member</em> shows that <em>"Count"</em> which is also the name of the column with the number of errors is a property. Sometimes the column names aren't the same as the property name. I sorted by <em>"Count"</em> in descending order to complete this one:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be5-5.png"><img class="alignnone size-full wp-image-3761" title="2012sg-be5-5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be5-5.png" width="640" height="187" /></a>
<pre class="lang:ps decode:true" title="Beginner Category Event #5 of the 2012 Scripting Games">Get-EventLog -ComputerName $Env:ComputerName -LogName Application -EntryType Error | Group-Object -Property Source -NoElement | Sort-Object -Property Count -Descending</pre>
View my entry for this event on the <a href="http://2012sg.poshcode.org/3910" target="_blank">PowerShell Code Repository site</a>.

µ