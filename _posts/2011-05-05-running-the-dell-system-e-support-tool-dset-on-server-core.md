---
ID: 2255
post_title: >
  Running the Dell System E-Support Tool
  (DSET) on Server Core
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/05/05/running-the-dell-system-e-support-tool-dset-on-server-core/
published: true
post_date: 2011-05-05 07:30:07
---
There are multiple reasons why you might want to run the Dell System E-Support Tool (DSET) on your Dell PowerEdge server. It could be because Dell Support has requested it or you’re trying to diagnose a problem or you simply want to know what version of BIOS your server is running without having to reboot it. This tool is especially useful when it comes to Windows Server 2008 R2 core installation since many other utilities will not run on server core.

The DSET utility can be installed on server core without a restart and without affecting production even if this is a Hyper-V server running multiple virtualization guest servers.

Download <a href="http://support.dell.com/support/topics/global.aspx/support/en/dell_system_tool" target="_blank">DSET</a> from the Dell support website.

You need to copy the downloaded MSI file to a temporary folder on the server or map a drive on the server to a network location from which the MSI file is accessible.

If you simply tried to run the MSI, you would receive an “Access Denied” error. This error also has nothing to do with <a href="http://en.wikipedia.org/wiki/User_Account_Control" target="_blank">User Account Control</a> (UAC).
<a href="http://mikefrobbins.com/wp-content/uploads/2011/05/dset1.png"><img class="alignnone size-full wp-image-2256" title="dset1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/05/dset1.png" width="297" height="94" /></a>

To install DSET on server core, you’ll need to use the unattended installation option:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/05/dset2.png"><img class="alignnone size-full wp-image-2257" title="dset2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/05/dset2.png" width="532" height="84" /></a>
<pre class="lang:batch decode:true">START /wait MSIEXEC.EXE /i Dell_DSET_1.9.0.131_A02.msi /qn</pre>
Once the installation is complete, navigate to the “C:\Program Files (x86)\Dell\DSET\bin&gt;” directory and execute the following which will create an advanced report and place it in the D:\tmp directory. If you want a basic report, do not specified the –advanced parameter (no parameter = basic report).
<a href="http://mikefrobbins.com/wp-content/uploads/2011/05/dset3.png"><img class="alignnone size-full wp-image-2258" title="dset3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/05/dset3.png" width="640" height="342" /></a>
<pre class="lang:batch decode:true">dellsysteminfo.exe -advanced d:\tmp</pre>
Copy and extract the report zip file that was created in the d:tmp folder to a machine with a GUI. The password for the report zip file is “dell”. Double-click on the dsetreport.hta file and your report full of all kinds of useful information will open in Internet Explorer:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/05/dset4.png"><img class="alignnone size-medium wp-image-2259" title="dset4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/05/dset4.png?w=258" width="258" height="300" /></a>

If you own Dell servers and you're not familiar with DSET, there is a PDF document about <a href="http://www.dell.com/content/topics/global.aspx/power/en/e_support_tool?c=us&amp;l=en" target="_blank">streamlining troubleshooting</a> with DSET on the Dell website that is a must read.

µ