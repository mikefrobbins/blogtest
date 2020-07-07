---
ID: 10264
post_title: >
  Exclude Delayed Start Services when
  Checking Status with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/07/31/exclude-delayed-start-services-when-checking-status-with-powershell/
published: true
post_date: 2014-07-31 07:30:20
---
While using PowerShell to return a list of Windows services that are set to startup automatically and aren't running may sound like a simple task, it's not quite as easy as it
sounds, at least not if you want accurate results. This is due to the services that are set to startup automatically with a delayed start skewing the results.

For more information on how to accurately return a list of services that should be running, but aren't based on services that have a startup type of automatic while excluding the ones that are set to start automatically with a delayed start, see <a href="http://blogs.technet.com/b/heyscriptingguy/archive/2014/07/31/exclude-delayed-start-services-when-checking-status-with-powershell.aspx" target="_blank">my article that is featured on the Hey, Scripting Guy! Blog</a> today.

It's now time for me to start packing up for my trip to LSU in Baton Rouge, Louisiana where I'll be presenting two PowerShell sessions at <a href="http://www.sqlsaturday.com/324/eventhome.aspx" target="_blank">SQL Saturday #324</a> this Saturday, August 2nd. I hope to see you there.

µ