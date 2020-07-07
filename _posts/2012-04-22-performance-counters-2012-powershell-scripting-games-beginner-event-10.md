---
ID: 3809
post_title: 'Performance Counters – 2012 PowerShell Scripting Games Beginner Event #10'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/04/22/performance-counters-2012-powershell-scripting-games-beginner-event-10/
published: true
post_date: 2012-04-22 07:30:55
---
The details of the event scenario and the design points for Beginner Event #10 of the 2012 PowerShell Scripting Games can be found on the “<a href="http://blogs.technet.com/b/heyscriptingguy/archive/2012/04/13/the-2012-scripting-games-beginner-event-10-collect-performance-counter-information.aspx" target="_blank">Hey, Scripting Guys! Blog</a>”.

Gather all of the counter information for the processor counter set. Take three separate readings at five second intervals. This information should be appended to a single text file named servername_ProcessorCounters.txt in the Documents special folder. You'll lose points for complexity. Use native PowerShell commands where possible.

This was the most confusing event for me since it specified "Processor Performance Counter Set" in one of the design points and my home pc with an AMD processor has a "Processor Performance" counter set:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be10-1.png"><img class="alignnone size-full wp-image-3927" title="2012sg-be10-1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be10-1.png" width="300" height="126" /></a>

Comparing the results of what the "Processor Performance" counter set returned on my pc to the screenshot provided in the scenario requirements assisted me in determining that what they were really asking for was the information in the "Processor" counter set.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be10-11.png"><img class="alignnone size-full wp-image-3931" title="2012sg-be10-11" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be10-11.png" width="562" height="546" /></a>

I spent a lot of time trying to figure out how to sort my data as shown in the screenshot provided in the scenario requirements of this one. I happened to come across a Hey, Scripting Guy! Blog article on "<a href="http://blogs.technet.com/b/heyscriptingguy/archive/2011/01/31/use-powershell-to-simplify-collecting-performance-information.aspx" target="_blank">Use PowerShell to Simplify Collecting Performance Information</a>" that showed how to accomplish the proper sorting.

The second difficult part was figuring out what the Documents special folder was? I found another blog article titled "<a href="http://blogs.technet.com/b/heyscriptingguy/archive/2012/02/20/the-easy-way-to-use-powershell-to-work-with-special-folders.aspx" target="_blank">The Easy Way to Use PowerShell to Work with Special Folders</a>" on the Hey, Scripting Guy! Blog that helped and then all I had to do was put the pieces together. I initially used "Combine" to combine the Documents special folder with the server name and file name but that didn't use native PowerShell commands. I also had to figure out that an escape character (the grave accent or back-tick) was needed after the $Env:ComputerName variable to use it as part of the file name as shown below:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be10-2.png"><img class="alignnone size-full wp-image-3917" title="2012sg-be10-2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be10-2.png" width="640" height="41" /></a>

While looking at other people's scripts for Beginner event #3, I found a reference to using <em>Join-Path</em> by one of the judges. I had used the same method as that person but didn't receive that sort of feedback so I was lucky I found this. Here's the script I submitted:
<pre class="lang:ps decode:true " title="Beginner Category Event #10 of the 2012 Scripting Games">Get-Counter -Counter (Get-Counter -ListSet Processor).Paths -SampleInterval 5 -MaxSamples 3 |
 Out-File -Append -FilePath (Join-Path -Path ([Environment]::GetFolderPath("MyDocuments")) -ChildPath "$Env:ComputerName`_ProcessorCounters.txt")</pre>
View my entry for this event on the <a href="http://2012sg.poshcode.org/5658" target="_blank">PowerShell Code Repository site</a>.

µ