---
ID: 13845
post_title: 'Video: Building Unconventional SQL Server Tools in PowerShell'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/04/15/video-building-unconventional-sql-server-tools-in-powershell/
published: true
post_date: 2016-04-15 07:30:19
---
Last week, on Wednesday (April 6th, 2016), I presented a session at the <a href="http://powershellsummit.org" target="_blank">PowerShell and DevOps Global Summit 2016</a> on "<em>Building Unconventional SQL Server Tools in PowerShell with Functions and Script Modules</em>". The <a href="https://www.youtube.com/watch?v=2rEzMWdTFDk" target="_blank">video</a> from that presentation is now available:

https://youtu.be/2rEzMWdTFDk

Here's the abstract or synopsis for this presentation: "<em>Have you ever had records from a SQL Server database table come up missing? Maybe someone or some process deleted them, but who really knows what happened to them? Wouldn’t it be awesome to create a free tool with PowerShell that automates the task of sifting through the transaction log backups and even the active transaction log to determine when deletes occurred for a specific database and what user deleted those records along with the LSN (Log Sequence Number) that the database needs to be restored to so the missing records can be recovered? During this session, PowerShell MVP Mike F Robbins will walk you through his custom SQL Server PowerShell module which includes functions for querying SQL Server with PowerShell from a client machine without requiring the installation of the SQL PowerShell module or snap-in, querying transaction logs for insert, update, and delete operations, and automated point in time recovery of a database by only having to specify the point in time to perform the recovery to. All code shown during this presentation will be made available on GitHub.</em>"

Also, if you haven't seen it, <a href="http://twitter.com/forrestbrazeal" target="_blank">Forrest Brazeal</a> wrote a nice critique of this session in his "<a href="https://forrestbrazeal.com/2016/04/06/notes-from-the-summit-day-3-summary/" target="_blank"><em>Notes from the Summit: Day 3</em></a>" blog article.

The slide deck and code sample shown in this presentation can be downloaded from <a href="https://github.com/mikefrobbins/Presentations" target="_blank">my presentations repository on GitHub</a>. All of the SQL related functions that are referenced during the demo can be downloaded as part of <a href="https://github.com/mikefrobbins/SQL" target="_blank">my SQL repository on GitHub</a>.

µ