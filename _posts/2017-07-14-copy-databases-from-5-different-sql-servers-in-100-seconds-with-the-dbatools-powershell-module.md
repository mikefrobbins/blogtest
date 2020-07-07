---
ID: 15392
post_title: >
  Copy databases from 5 different SQL
  Servers in 100 seconds with the DBATools
  PowerShell Module
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/07/14/copy-databases-from-5-different-sql-servers-in-100-seconds-with-the-dbatools-powershell-module/
published: true
post_date: 2017-07-14 07:30:57
---
As referenced in <a href="http://mikefrobbins.com/2017/07/13/presenting-powershell-101-the-no-nonsense-beginners-guide-to-powershell-this-weekend-at-sql-saturday-atlanta/" target="_blank" rel="noopener">my blog article from yesterday</a>, I'll be presenting a <a href="http://www.sqlsaturday.com/652/Sessions/Details.aspx?sid=64725" target="_blank" rel="noopener">PowerShell 101 session</a> at <a href="http://www.sqlsaturday.com/652/eventhome.aspx" target="_blank" rel="noopener">SQL Saturday Atlanta</a> tomorrow morning (July 15, 2017). While I plan to cover the basics of PowerShell, I also plan to show you what you can do with PowerShell without having to write very much code at all.

I'll be killing it in my session with live demos including this one that I've made a video of as a sneak preview.

https://youtu.be/wBEqHwmM-z8

If the video embedded in this webpage doesn't display properly, you can <a href="https://youtu.be/wBEqHwmM-z8" target="_blank" rel="noopener">find it on YouTube using this URL</a>. Oh, by the way, the title to this blog article is a little misleading since the entire video is 100 seconds. The actual time to copy the databases is much less.

The video starts out by checking the default instance of SQL Server on a server named SQL17 to see if any user databases exist. Then the names of five different SQL Servers are piped to ForEach-Object. Within the ForEach-Object loop, $_ is a variable for the current object. It's translated to each individual server name as it iterates through the list of SQL Servers, copying the user databases to SQL17. Only one user database exists on each of the source SQL Servers. The databases are backed up to the specified network share and restored to the destination server. The network share and any sub-folders that are specified must already exist. The account that SQL Server runs as on each of the servers must also have access to the network share. The names of the SQL Servers used in the demo correspond to the version of SQL Server they're running. The SQL05 server is running Windows Server 2008 (non-R2) and does not have any version of PowerShell installed which means the <em>Copy-SqlDatabase</em> function is extremely versatile.

PowerShell has been around for over 10 years now which means that someone has probably already written the code for whatever it is that you're trying to accomplish. Using modules such as the DBATools PowerShell module that's demonstrated in this teaser video, you can accomplish some amazing things without having to reinvent the wheel. More information about the DBATools module can be found at <a href="https://dbatools.io/" target="_blank" rel="noopener">dbatools.io</a>.

µ