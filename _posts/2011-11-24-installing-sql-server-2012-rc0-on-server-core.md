---
ID: 3119
post_title: >
  Installing SQL Server 2012 RC0 on Server
  Core
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/11/24/installing-sql-server-2012-rc0-on-server-core/
published: true
post_date: 2011-11-24 07:30:21
---
Last week, I published a blog on <a href="http://mikefrobbins.com/2011/11/17/installing-sql-server-denali-ctp3-on-server-core/" target="_blank">Installing SQL Server Denali CTP3 on Server Core</a> and then <a href="http://www.microsoft.com/sqlserver/en/us/future-editions.aspx" target="_blank">SQL Server 2012 RC0</a> was made available for download so I thought I’d write an updated blog since the issues I ran into with the installation seem to be resolved.

One of the new features in SQL Server 2012 is official support from Microsoft for installation on Server Core. There’s an MSDN article on “<a href="http://msdn.microsoft.com/en-us/library/hh231669(v=sql.110).aspx#bk_configurationfiles" target="_blank">Install SQL Server 2012 on Server Core</a>” and another MSDN article on “<a href="http://msdn.microsoft.com/en-us/library/ms144259(v=sql.110).aspx#Role" target="_blank">Install SQL Server 2012 from the Command Prompt</a>“ that has a detailed list of all the different parameters. There's also some good information about the installation on server core in the <a href="http://social.technet.microsoft.com/wiki/contents/articles/microsoft-sql-server-2012-rc-0-release-notes.aspx" target="_blank">release notes</a>.

If you just run setup from the command prompt, you’ll end up with this message, although this is to be expected since there are some required parameters which must be specified at the command line or in a configuration file.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/sql2012rc0-1.png"><img class="alignnone size-full wp-image-3120" title="sql2012rc0-1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/sql2012rc0-1.png" width="640" height="267" /></a>

One of the interesting new parameters as referenced in the image above is the /UIMODE=EnableUIOnServerCore option which launches the full blown installation GUI. I was unable to do the install through it so this functionality doesn't seem to be complete.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/sql2012rc0-1a.png"><img class="alignnone size-full wp-image-3129" title="sql2012rc0-1a" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/sql2012rc0-1a.png" width="640" height="502" /></a>

Based on the first article, the setup routine should enable and/or install all of the necessary prerequisites and with RC0 it does indeed do that (unlike Denali CTP3).
<pre class="lang:batch decode:true">Setup.exe /qs /ACTION=Install /FEATURES=SQLEngine /INSTANCENAME=MSSQLSERVER /SQLSVCACCOUNT="mikefrobbins\sqlSvcAcct" /SQLSVCPASSWORD="password" /AGTSVCACCOUNT="mikefrobbins\sqlAgentAcct" /AGTSVCPASSWORD="password" /AGTSVCSTARTUPTYPE="Automatic" /SQLSYSADMINACCOUNTS="mikefrobbins\administrator" /IACCEPTSQLSERVERLICENSETERMS</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/sql2012rc0-2.png"><img class="alignnone size-full wp-image-3121" title="sql2012rc0-2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/sql2012rc0-2.png" width="640" height="224" /></a>

The installation proceeds without any problems in the RC0 version:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/sql2012rc0-3.png"><img class="alignnone size-full wp-image-3122" title="sql2012rc0-3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/sql2012rc0-3.png" width="640" height="392" /></a>

The GUI portion of setup will begin, although it is unattended.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/sql2012rc0-4.png"><img class="alignnone size-full wp-image-3123" title="sql2012rc0-4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/sql2012rc0-4.png" width="640" height="137" /></a>

When the installation completes, you’ll be back at the command prompt:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/sql2012rc0-5.png"><img class="alignnone size-full wp-image-3124" title="sql2012rc0-5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/sql2012rc0-5.png" width="640" height="317" /></a>

Add a firewall exception for SQL Server.  Since there’s no GUI for this on server core, you’ll need to use the netsh command:
<pre class="lang:batch decode:true">netsh advfirewall firewall add rule name="SQL Server Windows NT - 64 Bit" dir=in action=allow program="C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe" enable=yes profile=domain</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core9a.png"><img class="alignnone size-full wp-image-3063" title="denali-core9a" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core9a.png" width="640" height="117" /></a>

With RC0 TCP/IP is enabled by default so there's no need to manually enable it:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core10a.png"><img class="alignnone size-full wp-image-3064" title="denali-core10a" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core10a.png" width="640" height="141" /></a>

You should now be able to use SQL Server Management Studio on a remote machine to access this SQL Server. If the connection fails, you can test connectivity from the remote machine by telneting to port 1433 on the sql server.

µ