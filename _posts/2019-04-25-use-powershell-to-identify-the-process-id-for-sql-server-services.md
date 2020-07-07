---
ID: 17788
post_title: >
  Use PowerShell to Identify the Process
  ID for SQL Server Services
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/04/25/use-powershell-to-identify-the-process-id-for-sql-server-services/
published: true
post_date: 2019-04-25 07:30:09
---
I recently saw a blog article on "<a href="https://blog.sqlauthority.com/2018/08/05/how-to-identify-process-id-for-sql-server-services-interview-question-of-the-week-185/" target="_blank" rel="noopener noreferrer">How to Identify Process ID for SQL Server Services? – Interview Question of the Week #185</a>" written by <a href="https://twitter.com/pinaldave" target="_blank" rel="noopener noreferrer">Pinal Dave</a>. While his answer is simple with TSQL, what if you're not a SQL guy? You can also retrieve this information with PowerShell from Windows itself.

When it comes to retrieving information about Windows services with PowerShell, the first command that comes to mind is <em>Get-Service</em>. Unfortunately, <em>Get-Service</em> doesn't return the process id for services. This means you'll need to resort to using the CIM cmdlets to retrieve this information.

This information can be retrieved from numerous remote SQL servers with the following command.
<pre class="lang:ps decode:true">Get-CimInstance -ComputerName sql14, sql16, sql17 -ClassName Win32_Service -Filter "Name like '%sql%' and state = 'running'" |
Format-Table -Property SystemName, Name, Caption, Status, ProcessId, StartName</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/04/sql-process-id1c.jpg"><img class="alignnone size-full wp-image-17793" src="https://mikefrobbins.com/wp-content/uploads/2019/04/sql-process-id1c.jpg" alt="" width="859" height="312" /></a>

One thing that I didn't retrieve with the previous command was the start time of the services which is a little more complicated. In a previous blog article, I wrote about how to "<a href="https://mikefrobbins.com/2017/11/30/determine-the-start-time-of-a-windows-service-with-powershell/" target="_blank" rel="noopener noreferrer">Determine the Start Time of a Windows Service with PowerShell</a>". I'll add that functionality in this example.
<pre class="lang:ps decode:true ">Invoke-Command -ComputerName sql14, sql16, sql17 {
    Get-CimInstance -ClassName Win32_Service -Filter "Name like '%sql%' and state = 'running'" -PipelineVariable Service |
    Select-Object -Property SystemName, Name, Caption, Status, ProcessId, StartName,
                            @{label='StartTime';expression={(Get-Process -Id $Service.ProcessId).StartTime}}
} | Format-Table -Property SystemName, Name, Caption, Status, ProcessId, StartTime, StartName</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/04/sql-process-id2a.jpg"><img class="alignnone size-full wp-image-17794" src="https://mikefrobbins.com/wp-content/uploads/2019/04/sql-process-id2a.jpg" alt="" width="859" height="354" /></a>

If you wanted to maximize the efficiency of the commands shown in this blog article, you could also specify the <em>Property</em> parameter with <em>Get-CimInstance</em> to only return specific properties instead of all of them.

µ