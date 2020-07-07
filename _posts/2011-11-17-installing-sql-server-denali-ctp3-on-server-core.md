---
ID: 3040
post_title: >
  Installing SQL Server Denali CTP3 on
  Server Core
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/11/17/installing-sql-server-denali-ctp3-on-server-core/
published: true
post_date: 2011-11-17 07:30:38
---
One of the new features in SQL Server Denali CTP3 is support for installation on Server Core. There’s an MSDN article on “<a href="http://msdn.microsoft.com/en-us/library/hh231669(v=sql.110).aspx#bk_configurationfiles" target="_blank">Installing SQL Server Denali on Server Core</a>” and another MSDN article on "<a href="http://msdn.microsoft.com/en-us/library/ms144259(v=sql.110).aspx#Role" target="_blank">Install SQL Server Denali from the Command Prompt</a>" that has a detailed list of all the different parameters.

Based on the first article, the setup routine should enable and/or install all of the necessary prerequisites, but that simply isn’t the case from what I found.

If you just run setup from the command prompt, you’ll end up with this message, although this is to be expected since there are some required parameters which must be specified at the command line or in a configuration file.<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core1a.png"><img class="alignnone size-full wp-image-3055" title="denali-core1a" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core1a.png" width="640" height="166" /></a>

If you specify all of the correct parameters, the command will return to the command prompt without error after a few seconds and nothing related to SQL will be installed.
<pre class="lang:batch decode:true">Setup.exe /qs /ACTION=Install /FEATURES=SQLEngine /INSTANCENAME=MSSQLSERVER /SQLSVCACCOUNT=
"mikefrobbins\sqlSvcAcct" /SQLSVCPASSWORD="password" /AGTSVCACCOUNT="mikefrobbins\sqlAgentAcct"
/AGTSVCPASSWORD="password" /AGTSVCSTARTUPTYPE="Automatic" /SQLSYSADMINACCOUNTS=
"mikefrobbins\administrator" /IACCEPTSQLSERVERLICENSETERMS</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core2a.png"><img class="alignnone size-full wp-image-3056" title="denali-core2a" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core2a.png" width="640" height="198" /></a>

Go ahead and start Powershell and import the Server Manager module:
<pre class="lang:ps decode:true">Import-Module ServerManager</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core3a.png"><img class="alignnone size-full wp-image-3057" title="denali-core3a" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core3a.png" width="502" height="111" /></a>

Enable the wow64-netfx3 feature:
<pre class="lang:ps decode:true">Add-WindowsFeature WoW64-NetFx3</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core4a.png"><img class="alignnone size-full wp-image-3058" title="denali-core4a" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core4a.png" width="640" height="118" /></a>

This will actually enable the following features:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core5a.png"><img class="alignnone size-full wp-image-3059" title="denali-core5a" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core5a.png" width="640" height="142" /></a>

Type exit from within PowerShell to get back to a normal command prompt and then run setup again with all of the necessary parameters. The installation should begin:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core6a.png"><img class="alignnone size-full wp-image-3060" title="denali-core6a" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core6a.png" width="640" height="312" /></a>

The GUI portion of setup will begin, although it is unattended.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core7a.png"><img class="alignnone size-full wp-image-3061" title="denali-core7a" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core7a.png" width="640" height="156" /></a>

When the installation completes, you’ll be back at the command prompt:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core2a.png"><img class="alignnone size-full wp-image-3056" title="denali-core2a" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core2a.png" width="640" height="198" /></a>

Add a firewall exception for SQL Server:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install111.png"><img class="alignnone size-full wp-image-2497" title="sp2010-install11" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install111.png" width="589" height="391" /></a>

Since there's no GUI for this on server core, you'll need to use the netsh command:
<pre class="lang:batch decode:true">netsh advfirewall firewall add rule name="SQL Server Windows NT - 64 Bit" dir=in action=allow program="C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe" enable=yes profile=domain</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core9a.png"><img class="alignnone size-full wp-image-3063" title="denali-core9a" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core9a.png" width="640" height="117" /></a>

Enable TCP/IP in SQL Server Configuration Manager:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install9.png"><img class="alignnone size-full wp-image-2495" title="sp2010-install9" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install9.png" width="442" height="189" /></a>

There's no way that I'm aware of to run SQL Server Configuration Manager on server core or to run it remotely so you'll have to manually edit the registry to enable TCP/IP. Run regedit, navigate to HKLM&gt;SoftwareMicrosoft&gt;Microsoft SQL Server&gt;MSSQL11.MSSQLSERVER&gt;MSSQLServer&gt;SuperSocketNetLib&gt;Tcp and set the value of enabled to 1:<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core10a.png"><img class="alignnone size-full wp-image-3064" title="denali-core10a" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/denali-core10a.png" width="640" height="141" /></a>

Restart the SQL Server service:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install10.png"><img class="alignnone size-full wp-image-2496" title="sp2010-install10" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install10.png" width="640" height="290" /></a>

You should now be able to use SQL Server Management Studio on a remote machine to access this SQL Server. If the connection fails, you can test connectivity from the remote machine by telneting to port 1433 on the sql server.

µ