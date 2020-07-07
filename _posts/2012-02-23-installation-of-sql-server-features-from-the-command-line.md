---
ID: 3318
post_title: >
  Installation of SQL Server Features from
  the Command Line
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/02/23/installation-of-sql-server-features-from-the-command-line/
published: true
post_date: 2012-02-23 07:30:15
---
I'm installing a line of business application that requires the SQL Server Management Studio and Client Tools Connectivity be installed as a prerequisite. I want to script this instead of having to manually click through the GUI for the installation since this will be installed on several servers. I've mounted the SQL Server 2008 R2 ISO on "Z" drive. The following command will produce the desired results:
<pre class="lang:batch decode:true">Setup.exe /qs /ACTION=Install /FEATURES=SSMS,CONN /IACCEPTSQLSERVERLICENSETERMS</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/ssms-conn11.png"><img class="alignnone size-full wp-image-3329" title="ssms-conn11" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/ssms-conn11.png" width="640" height="86" /></a>

At first, I thought that the CONN parameter was incorrect because it also added the  SQL Client Connectivity SDK, but upon further review the GUI installation does the same thing:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/ssms-conn12.png"><img class="alignnone size-full wp-image-3330" title="ssms-conn12" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/ssms-conn12.png" width="395" height="312" /></a>

Using the command line or the GUI for the installation will show that the SQL Client Connectivity SDK was installed:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/ssms-conn13.png"><img class="alignnone size-full wp-image-3331" title="ssms-conn13" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/ssms-conn13.png" width="243" height="313" /></a>

Let's say you're not sure of the feature names of the options you want to install, you want more than these two options installed, or you want to simplify this process even more. Use the GUI on the first server to do the installation. This will create a configuration ini file:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/ssms-conn14.png"><img class="alignnone size-full wp-image-3332" title="ssms-conn14" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/ssms-conn14.png" width="512" height="441" /></a>

Copy this configuration file and use it on the other servers to do an unattended installation. You'll need to change the QUIETSIMPLE parameter to True and remark out the UIMODE property by placing a semicolon at the beginning of that line. Specifying the /QS parameter from the command line won't work since those options are set in the ini file:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/ssms-conn15.png"><img class="alignnone size-full wp-image-3333" title="ssms-conn15" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/ssms-conn15.png" width="567" height="202" /></a>
<pre class="lang:batch decode:true">Setup.exe /CONFIGURATIONFILE="C:\tmp\ConfigurationFile.ini" /IACCEPTSQLSERVERLICENSETERMS</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/ssms-conn16.png"><img class="alignnone size-full wp-image-3334" title="ssms-conn16" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/ssms-conn16.png" width="640" height="291" /></a>

A list of the parameters to install SQL Server 2012 from the command line can be found on <a href="http://msdn.microsoft.com/en-us/library/ms144259(v=sql.110).aspx" target="_blank">MSDN</a>.

µ