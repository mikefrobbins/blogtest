---
ID: 14135
post_title: >
  Learn Building Unconventional SQL Server
  Tools in PowerShell this weekend at SQL
  Saturday Chattanooga 2016
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/06/23/learn-building-unconventional-sql-server-tools-in-powershell-this-weekend-at-sql-saturday-chattanooga-2016/
published: true
post_date: 2016-06-23 07:30:56
---
I’m presenting a session on Building Unconventional SQL Server Tools in PowerShell this weekend at <a href="http://www.sqlsaturday.com/498/eventhome.aspx" target="_blank">SQL Saturday #498 in Chattanooga</a>.

<a href="http://mikefrobbins.com/wp-content/uploads/2016/05/interop.jpg"><img class="alignnone size-full wp-image-14073" src="http://mikefrobbins.com/wp-content/uploads/2016/05/interop.jpg" alt="interop" width="890" height="434" /></a>

Ever had records from a SQL Server database table come up missing? Maybe someone or some process deleted them, but who really knows what happened to them? Wouldn’t it be awesome to create a free tool with PowerShell that automates the task of sifting through the transaction log backups and even the active transaction log to determine when deletes occurred for a specific database and what user deleted those records along with the Log Sequence Number that the database needs to be restored to so the missing records can be recovered? During this session, PowerShell MVP Mike F Robbins will walk you through his custom SQL PowerShell module which includes functions for querying SQL Server with PowerShell from a client machine without requiring the installation of the SQL PowerShell module or snap-in, querying transaction logs for insert, update, and delete operations, and automated point in time recovery of a database by only having to specify the point in time to perform the recovery to.

My session begins at 8:15am in room 201 in the EMC Building at UT Chattanooga on Saturday, June 25th. Be sure to check <a href="http://www.sqlsaturday.com/498/Sessions/Schedule.aspx" target="_blank">the event schedule</a> for any schedule changes between now and then.

The slide deck and demo code will be posted in <a href="https://github.com/mikefrobbins/Presentations" target="_blank">my presentations repository on GitHub</a> prior to the session in case anyone wants to follow along during the presentation.

µ