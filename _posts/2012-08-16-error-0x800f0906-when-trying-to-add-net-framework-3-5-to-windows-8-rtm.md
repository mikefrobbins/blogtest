---
ID: 5163
post_title: >
  Error 0x800F0906 When Trying To Add .NET
  Framework 3.5 to Windows 8 RTM
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/08/16/error-0x800f0906-when-trying-to-add-net-framework-3-5-to-windows-8-rtm/
published: true
post_date: 2012-08-16 14:40:07
---
The Windows 8 RTM was released yesterday to TechNet subscribers and this morning I decided to start the day off by reloading my business computer. I've been running the Release Preview on a netbook and on some VM's without issue. Everything seemed to go well until I tried to load several applications that required the .NET Framework 3.5. Each of them failed to install stating they were unable to add the .NET framework so I decided to install it manually. Here's the process I used to try to install it through the GUI:

I started out by selecting the ".NET Framework 3.5 (includes .NET 2.0 and 3.0) feature:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-net35error1.jpg"><img class="alignnone size-full wp-image-5165" title="win8-net35error1" src="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-net35error1.jpg" alt="" width="428" height="375" /></a>

I selected "Download files from Windows Update":

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-net35error2.jpg"><img class="alignnone size-full wp-image-5166" title="win8-net35error2" src="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-net35error2.jpg" alt="" width="584" height="473" /></a>

Here's the error message I received "Windows couldn't complete the requested changes. Windows couldn't connect to the Internet to download necessary files. Make sure that you're connected to the Internet, and click "Retry" to try again. Error code: 0x800F0906"

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-net35error3.jpg"><img class="alignnone size-full wp-image-5167" title="win8-net35error3" src="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-net35error3.jpg" alt="" width="582" height="473" /></a>

After clicking "Close" as shown in the previous image, I received the following error: "An error has occurred. Not all of the features were successfully changed.":

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-net35error4.jpg"><img class="alignnone size-full wp-image-5169" title="win8-net35error4" src="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-net35error4.jpg" alt="" width="455" height="131" /></a>

I also tried to enable the .NET Framework 3.5 feature by using PowerShell which produced another error:
"<em><span style="color: #ff0000;">Enable-WindowsOptionalFeature : The source files could not be downloaded. </span></em>
<em> <span style="color: #ff0000;">Use the "source" option to specify the location of the files that are required to restore the feature. For more information on specifying a source </span></em>
<em> <span style="color: #ff0000;">location, see http://go.microsoft.com/fwlink/?LinkId=243077.</span></em>
<em> <span style="color: #ff0000;">At line:1 char:1</span></em>
<em> <span style="color: #ff0000;">+ Enable-WindowsOptionalFeature -Online -FeatureName 'NetFx3'</span></em>
<em> <span style="color: #ff0000;">+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~</span></em>
<em> <span style="color: #ff0000;"> + CategoryInfo : NotSpecified: (:) [Enable-WindowsOptionalFeature], COMException</span></em>
<em> <span style="color: #ff0000;"> + FullyQualifiedErrorId : Microsoft.Dism.Commands.EnableWindowsOptionalFeatureCommand</span></em>":

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-net35error5.jpg"><img class="alignnone size-full wp-image-5170" title="win8-net35error5" src="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-net35error5.jpg" alt="" width="640" height="117" /></a>

I was able to finally resolve this problem by mounting an ISO of Windows 8 on "E" drive and running the following PowerShell command:
<pre class="lang:ps decode:true">Enable-WindowsOptionalFeature -Online -FeatureName 'NetFx3' -Source 'E:\sources\sxs'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-net35error6.jpg"><img class="alignnone size-full wp-image-5171" title="win8-net35error6" src="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-net35error6.jpg" alt="" width="640" height="69" /></a>

µ