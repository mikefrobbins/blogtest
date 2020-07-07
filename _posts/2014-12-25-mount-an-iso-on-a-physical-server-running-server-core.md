---
ID: 10407
post_title: >
  Mount an ISO on a Physical Server
  running Server Core
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/12/25/mount-an-iso-on-a-physical-server-running-server-core/
published: true
post_date: 2014-12-25 07:30:52
---
So you need to mount an ISO on a physical server that is running Windows Server 2012 R2 Server Core? If so, how would you accomplish that task without a graphical user interface?

With PowerShell of course, specifically the <a href="http://technet.microsoft.com/en-us/library/hh848706.aspx" target="_blank"><em>Mount-DiskImage</em></a> PowerShell cmdlet:
<pre class="lang:ps decode:true">Mount-DiskImage -ImagePath 'D:\ISO\Windows Server 2012 Trial\9200.16384.WIN8_RTM.120725-1247_X64FRE_SERVER_EVAL_EN-US-HRM_SSS_X64FREE_EN-US_DV5.ISO' -StorageType ISO -PassThru</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/mount-iso1.png"><img class="alignnone size-full wp-image-10422" src="http://mikefrobbins.com/wp-content/uploads/2014/09/mount-iso1.png" alt="mount-iso1" width="875" height="263" /></a>

By default nothing is returned when using the <em>Mount-DiskImage</em> cmdlet and the ISO is mounted using the next available drive letter. The <em>-PassThru</em> parameter was specified in the previous example but notice there's no property that that tells us which drive letter was assigned.

Let's see if we can figure out a way to make it tell us what drive letter was assigned. Piping the previous command to <em>Get-Member</em> confirms that there isn't a drive letter property:
<pre class="lang:ps decode:true">Mount-DiskImage -ImagePath 'D:\ISO\Windows Server 2012 Trial\9200.16384.WIN8_RTM.120725-1247_X64FRE_SERVER_EVAL_EN-US-HRM_SSS_X64FREE_EN-US_DV5.ISO' -StorageType ISO -PassThru | Get-Member</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/mount-iso2.png"><img class="alignnone size-full wp-image-10423" src="http://mikefrobbins.com/wp-content/uploads/2014/09/mount-iso2.png" alt="mount-iso2" width="875" height="413" /></a>

One thing to keep in mind is that if we didn't use the <em>-PassThru</em> parameter to make the command produce output, piping it to <em>Get-Member</em> would generate an error because only commands that produce output can be piped to the <em>Get-Member</em> cmdlet.

Notice that piping the command to <em>Get-Member</em> as shown in the previous example also gave us the type of object that it produced which is specified after "<em>TypeName:</em>" and before the list of methods and properties.

Maybe there's something we can pipe the results of <em>Mount-DiskImage</em> to that will tell us what drive letter is assigned? We can use <em>Get-Command</em> with the <em>-ParameterType</em> parameter to determine what cmdlets accept input from <em>MSFT_DiskImage</em> objects:
<pre class="lang:ps decode:true">Get-Command -ParameterType MSFT_DiskImage</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/mount-iso3.png"><img class="alignnone size-full wp-image-10424" src="http://mikefrobbins.com/wp-content/uploads/2014/09/mount-iso3.png" alt="mount-iso3" width="875" height="154" /></a>

<a href="http://technet.microsoft.com/en-us/library/hh848646.aspx" target="_blank"><em>Get-Volume</em></a> looks promising, but let's verify that it accepts MSFT_DiskImage objects via pipeline input because cmdlets that accept that type of object via parameter and/or pipeline input would show up in the previous set of results.

Looking at the "INPUTS" section of the help for <em>Get-Volume</em> confirms that it does indeed accept pipeline input for MSFT_DiskImage objects:
<pre class="lang:ps decode:true  ">help Get-Volume -Full</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/12/mount-iso5.png"><img class="alignnone size-full wp-image-10963" src="http://mikefrobbins.com/wp-content/uploads/2014/12/mount-iso5.png" alt="mount-iso5" width="877" height="482" /></a>

Let's pipe our <em>Mount-DiskImage</em> command to <em>Get-Volume</em> and see what happens:
<pre class="lang:ps decode:true">Mount-DiskImage -ImagePath 'D:\ISO\Windows Server 2012 Trial\9200.16384.WIN8_RTM.120725-1247_X64FRE_SERVER_EVAL_EN-US-HRM_SSS_X64FREE_EN-US_DV5.ISO' -StorageType ISO -PassThru | Get-Volume</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/mount-iso4.png"><img class="alignnone size-full wp-image-10425" src="http://mikefrobbins.com/wp-content/uploads/2014/09/mount-iso4.png" alt="mount-iso4" width="875" height="144" /></a>

As you can see in the previous set of results, piping <em>Mount-DiskImage</em> to <em>Get-Volume</em> allows us to see what drive letter was assigned. In the previous example, drive letter 'E' was assigned to the ISO image that was mounted.

µ