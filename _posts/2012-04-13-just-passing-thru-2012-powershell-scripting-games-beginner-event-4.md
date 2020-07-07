---
ID: 3669
post_title: 'Just Passing Thru &#8211; 2012 PowerShell Scripting Games Beginner Event 4'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/04/13/just-passing-thru-2012-powershell-scripting-games-beginner-event-4/
published: true
post_date: 2012-04-13 07:30:20
---
The details of the event scenario and the design points for Beginner Event #4 of the 2012 PowerShell Scripting Games can be found on the “<a href="http://blogs.technet.com/b/heyscriptingguy/archive/2012/04/05/2012-scripting-games-beginner-event-4-compare-two-folders.aspx" target="_blank">Hey, Scripting Guys! Blog</a>”.

The key to this one is figuring out how to format the output as shown in the screen shot in the event scenario which is similar to the one in the image below:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be4-1.png"><img class="alignnone size-full wp-image-3734" title="2012sg-be4-1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be4-1.png" width="379" height="202" /></a>

The <a href="http://powershelldownunder.com/" target="_blank">PowerShell Down Under</a> guys posted some great prep videos leading up to the beginning of the scripting games and one of them titled "<a href="http://powershelldownunder.com/2012/03/19/scripting-games-2012working-with-folders/" target="_blank">Scripting Games 2012 - Working with Folders</a>" gives you a head start on solving this one. I found out about these video's by following other PowerShell enthusiast on <a href="http://twitter.com/search/users/powershell" target="_blank">Twitter</a>. If you've read Don Jones's book titled "<a href="http://www.manning.com/jones/" target="_blank">Learn Windows PowerShell in a Month of Lunches</a>", he also helps prepare you for this one. The one exception is using the <em>-Passthru</em> parameter to format the output properly. I hadn't previously seen any references to this parameter being used with the <em>Compare-Object</em> cmdlet but I had seen it used with other cmdlets. Getting the help information on <em>Compare-Object</em> showed this parameter existed and trial &amp; error confirmed that it formated the output properly.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be4-2.png"><img class="alignnone size-full wp-image-3735" title="2012sg-be4-2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/2012sg-be4-2.png" width="640" height="117" /></a>

Here's the one liner that I submitted to the <a href="http://2012sg.poshcode.org/3575" target="_blank">PowerShell Code Repository site</a>:
<pre class="lang:ps decode:true" title="Beginner Category Event #4 of the 2012 Scripting Games">Compare-Object -ReferenceObject (Get-ChildItem 'c:\2') -DifferenceObject (Get-ChildItem 'c:\1') -Property Name -PassThru</pre>
µ