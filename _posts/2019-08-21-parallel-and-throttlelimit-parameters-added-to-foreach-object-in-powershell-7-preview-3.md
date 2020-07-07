---
ID: 18056
post_title: >
  Parallel and ThrottleLimit Parameters
  added to ForEach-Object in PowerShell 7
  Preview 3
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/08/21/parallel-and-throttlelimit-parameters-added-to-foreach-object-in-powershell-7-preview-3/
published: true
post_date: 2019-08-21 07:30:17
---
Preview 3 of PowerShell 7 was released yesterday. It can be downloaded from <a href="https://github.com/PowerShell/PowerShell" target="_blank" rel="noopener noreferrer">the PowerShell repository on GitHub</a>. Be sure to choose the preview version for the appropriate operating system. It should go without saying that anything with preview in its name should NOT be installed on a mission-critical production system.

The examples shown in this blog article are being run on a system running Windows 10 (x64). Your mileage may vary with other operating systems and/or versions.
<pre class="lang:ps decode:true">$PSVersionTable.PSVersion</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/pwsh7-preview3.jpg"><img class="alignnone size-full wp-image-18057" src="https://mikefrobbins.com/wp-content/uploads/2019/08/pwsh7-preview3.jpg" alt="" width="859" height="131" /></a>

One of the hot new features in this release is the addition of the ability to run <em>ForEach-Object</em> in parallel with the new <em>Parallel</em> parameter. This is different than running <em>foreach</em> in parallel in a workflow in Windows PowerShell.

First, I'll start by using <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_operators?view=powershell-6#range-operator-" target="_blank" rel="noopener noreferrer">the range operator</a> to simply sleep 10 times for one second each as shown in the following example. <em>Measure-Command</em> is used to determine how long the command takes and I'll use the dotted-notation syntax style to return only the total seconds.
<pre class="lang:ps decode:true">(Measure-Command {
    1..10 | ForEach-Object {Start-Sleep -Seconds 1}
}).TotalSeconds</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/foreach-object-parallel1a.jpg"><img class="alignnone size-full wp-image-18058" src="https://mikefrobbins.com/wp-content/uploads/2019/08/foreach-object-parallel1a.jpg" alt="" width="859" height="104" /></a>

The same command is used in the next example except the <em>Parallel</em> parameter has been specified. You would think this should finish is about a second or so depending on the overhead, but over two seconds seems too long, although it is indeed roughly 5 times faster than the previous example.
<pre class="lang:ps decode:true">(Measure-Command {
    1..10 | ForEach-Object -Parallel {Start-Sleep -Seconds 1}
}).TotalSeconds</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/foreach-object-parallel2a.jpg"><img class="alignnone size-full wp-image-18059" src="https://mikefrobbins.com/wp-content/uploads/2019/08/foreach-object-parallel2a.jpg" alt="" width="859" height="104" /></a>

The answer to why the previous command took over two seconds is because a <em>ThrottleLimit</em> parameter has also been added and it defaults to a limit of 5 threads at a time. Divide the 10 times the previous command is being run by the throttle limit of 5 and that's why it took over two seconds for the previous example to complete.

In the next example, I'll specify 10 as the value for the <em>ThrottleLimit</em> parameter since I know the command is going to run 10 times.
<pre class="lang:ps decode:true">(Measure-Command {
    1..10 | ForEach-Object -Parallel {Start-Sleep -Seconds 1} -ThrottleLimit 10
}).TotalSeconds</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/foreach-object-parallel3a.jpg"><img class="alignnone size-full wp-image-18060" src="https://mikefrobbins.com/wp-content/uploads/2019/08/foreach-object-parallel3a.jpg" alt="" width="859" height="104" /></a>

Now those results are more like what was expected. Run something for one second 10 times in parallel and it takes about a second plus some overhead to complete.

In addition to the parameters shown in this blog article, <em>ForEach-Object</em> also adds <em>TimeoutSeconds</em> and <em>AsJob</em> parameters in PowerShell 7 Preview 3.

You can find more information about what's new in PowerShell 7 Preview 3 in <a href="https://devblogs.microsoft.com/powershell/powershell-7-preview-3/" target="_blank" rel="noopener noreferrer">this article</a> that was posted on <a href="https://devblogs.microsoft.com/powershell/" target="_blank" rel="noopener noreferrer">the PowerShell team blog</a> yesterday.

Thoughts, questions, comments? Please post them as a comment to this blog article.

µ