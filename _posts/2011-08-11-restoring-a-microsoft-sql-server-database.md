---
ID: 2672
post_title: >
  Restoring a Microsoft SQL Server
  Database
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/08/11/restoring-a-microsoft-sql-server-database/
published: true
post_date: 2011-08-11 07:30:51
---
This blog article is a scenario that I sent a coworker a while back about recovering a SQL Server database up to the point in time where a catastrophic hard disk drive failure occurs for the hard drive containing a SQL Server database. The transaction log for this database is on a separate physical disk and is still accessible.

I added the Northwind database to SQL Express on my machine, changed the recovery model to full, and then backed up the database and transaction log:
<pre class="lang:tsql decode:true">BACKUP DATABASE Northwind TO DISK = 'C:\Program Files\Microsoft SQL Server\MSSQL10.SQLEXPRESS\MSSQL\Backup\Northwind.bak'
WITH DESCRIPTION = 'Northwind Full Backup', NAME = 'Full Backup'
GO
BACKUP LOG Northwind TO DISK = 'C:\Program Files\Microsoft SQL Server\MSSQL10.SQLEXPRESS\MSSQL\Backup\Northwind1.trn'
WITH DESCRIPTION = 'Northwind Log Backup', NAME = 'Log Backup'
GO</pre>
<em>Processed 392 pages for database 'Northwind', file 'Northwind' on file 1.</em>
<em> Processed 2 pages for database 'Northwind', file 'Northwind_log' on file 1.</em>
<em> BACKUP DATABASE successfully processed 394 pages in 0.922 seconds (3.331 MB/sec).</em>
<em> Processed 2 pages for database 'Northwind', file 'Northwind_log' on file 1.</em>
<em> BACKUP LOG successfully processed 2 pages in 0.074 seconds (0.171 MB/sec).</em>

I modified a row of data:
<pre class="lang:tsql decode:true">UPDATE Categories
SET CategoryName = 'Drinks'
WHERE CategoryID = '1'</pre>
I stopped the SQL Server service and deleted the Northwind.mdf file and then restarted the SQL Server service, simulating a disk drive failure for the drive that contains the Northwind database. Keep in mind that the transaction log is on a separate physical disk.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/08/db-restore1.jpg"><img class="alignnone size-full wp-image-2674" title="db-restore1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/08/db-restore1.jpg" width="551" height="123" /></a>

I backed up the transaction log since it was still accessible:
<pre class="lang:tsql decode:true">BACKUP LOG Northwind TO DISK = 'C:\Program Files\Microsoft SQL Server\MSSQL10.SQLEXPRESS\MSSQL\Backup\Northwind2.trn'
WITH CONTINUE_AFTER_ERROR, DESCRIPTION = 'Northwind Log Backup - Tail', NAME = 'Log Backup'</pre>
<em>Processed 9 pages for database 'Northwind', file 'Northwind_log' on file 1.</em>
<em> BACKUP LOG successfully processed 9 pages in 0.045 seconds (1.562 MB/sec).</em>

I restored the database and applied all of the transaction log backups:
<pre class="lang:tsql decode:true">RESTORE DATABASE Northwind
FROM DISK = 'C:\Program Files\Microsoft SQL Server\MSSQL10.SQLEXPRESS\MSSQL\Backup\Northwind.bak'
WITH NORECOVERY
GO
RESTORE LOG Northwind
FROM DISK = 'C:\Program Files\Microsoft SQL Server\MSSQL10.SQLEXPRESS\MSSQL\Backup\Northwind1.trn'
WITH NORECOVERY
GO
RESTORE LOG Northwind
FROM DISK = 'C:\Program Files\Microsoft SQL Server\MSSQL10.SQLEXPRESS\MSSQL\Backup\Northwind2.trn'
WITH NORECOVERY
GO
RESTORE DATABASE Northwind WITH RECOVERY
GO</pre>
<em>Processed 392 pages for database 'Northwind', file 'Northwind' on file 1.</em>
<em> Processed 2 pages for database 'Northwind', file 'Northwind_log' on file 1.</em>
<em> RESTORE DATABASE successfully processed 394 pages in 0.335 seconds (9.168 MB/sec).</em>
<em> Processed 0 pages for database 'Northwind', file 'Northwind' on file 1.</em>
<em> Processed 2 pages for database 'Northwind', file 'Northwind_log' on file 1.</em>
<em> RESTORE LOG successfully processed 2 pages in 0.012 seconds (1.057 MB/sec).</em>
<em> Processed 0 pages for database 'Northwind', file 'Northwind' on file 1.</em>
<em> Processed 9 pages for database 'Northwind', file 'Northwind_log' on file 1.</em>
<em> RESTORE LOG successfully processed 9 pages in 0.020 seconds (3.515 MB/sec).</em>
<em> RESTORE DATABASE successfully processed 0 pages in 0.512 seconds (0.000 MB/sec).</em>

I validated my data changes which were only part of the transaction log backup that occurred after the simulated hard disk drive failure were restored to the database:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/08/db-restore2.jpg"><img class="alignnone size-full wp-image-2675" title="db-restore2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/08/db-restore2.jpg" width="640" height="35" /></a>

Pretty cool, huh?

µ