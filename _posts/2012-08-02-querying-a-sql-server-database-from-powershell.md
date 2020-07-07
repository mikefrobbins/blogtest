---
ID: 5055
post_title: >
  Querying a SQL Server Database from
  PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/08/02/querying-a-sql-server-database-from-powershell/
published: true
post_date: 2012-08-02 07:30:32
---
A few days ago, I wrote a PowerShell script on my computer that would ultimately be used on a different computer to automate a specific task. One of the things this script did was to query a SQL Server database which worked fine on my computer. After moving the script, it didn't take long to figure out that the other computer didn't have the necessary SQL PowerShell snap-in or module. My goal was to install the minimum features of SQL Server 2008 R2 to be able to run Transact SQL from PowerShell against a remote SQL Server.

For SQL Server 2008 R2, the "Management Tools - Basic" is all that is needed for the SQL PowerShell snap-ins to be installed:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/sql-ps-snapin1.jpg"><img class="alignnone size-full wp-image-5057" title="sql-ps-snapin1" src="http://mikefrobbins.com/wp-content/uploads/2012/08/sql-ps-snapin1.jpg" alt="" width="640" height="480" /></a>

If you're installing from the command line, it's the "SSMS" feature:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/sql-ps-snapin2.jpg"><img class="alignnone size-full wp-image-5058" title="sql-ps-snapin2" src="http://mikefrobbins.com/wp-content/uploads/2012/08/sql-ps-snapin2.jpg" alt="" width="640" height="318" /></a>

Performing this install from either the GUI or command line, will also automatically add the "SQL Client Connectivity SDK":

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/sql-ps-snapin3.jpg"><img class="alignnone size-full wp-image-5059" title="sql-ps-snapin3" src="http://mikefrobbins.com/wp-content/uploads/2012/08/sql-ps-snapin3.jpg" alt="" width="640" height="480" /></a>

The SQL PowerShell snap-ins are installed and available to be added via the <em>Add-PSSnapin</em> PowerShell cmdlet:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/sql-ps-snapin41.jpg"><img class="alignnone size-full wp-image-5064" title="sql-ps-snapin41" src="http://mikefrobbins.com/wp-content/uploads/2012/08/sql-ps-snapin41.jpg" alt="" width="609" height="140" /></a>

I'm now able to query a SQL Server database on a remote server using PowerShell:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/sql-ps-snapin5.jpg"><img class="alignnone size-full wp-image-5063" title="sql-ps-snapin5" src="http://mikefrobbins.com/wp-content/uploads/2012/08/sql-ps-snapin5.jpg" alt="" width="633" height="216" /></a>

With SQL 2012, the SQL PowerShell Snap-ins are replaced with SQL PowerShell Modules.

µ