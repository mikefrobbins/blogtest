---
ID: 3602
post_title: >
  Confusion on PowerShell Parameterized
  Functions
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/03/20/confusion-on-powershell-parameterized-functions/
published: true
post_date: 2012-03-20 21:12:30
---
I've run into an issue with PowerShell functions that I don't understand. I guess my confusion is most of the examples I've found online and in books don't work the way the authors explained (at least not for me).

The problem is when I define a parameter inside a function, that parameter is not accessible when running the script.
<pre class="lang:ps decode:true">function Get-CPUInfo {
 Param ($ComputerName)
Get-WmiObject -Class Win32_Processor -ComputerName $ComputerName |
Select-Object SystemName, CurrentClockSpeed, Manufacturer |
Format-Table -AutoSize
}
Get-CPUInfo</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps-function1.png"><img class="alignnone size-full wp-image-3603" title="ps-function1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps-function1.png" width="640" height="201" /></a>

When I move the parameter outside of the function, it's accessible and it works without issue. I'm guessing this is because of the scope of the variable?
<pre class="lang:ps decode:true">Param ($ComputerName)
function Get-CPUInfo {
Get-WmiObject -Class Win32_Processor -ComputerName $ComputerName |
Select-Object SystemName, CurrentClockSpeed, Manufacturer |
Format-Table -AutoSize
}
Get-CPUInfo</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps-function2.png"><img class="alignnone size-full wp-image-3604" title="ps-function2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps-function2.png" width="518" height="216" /></a>

Is this how parameters are suppose to work or am I doing something wrong? Any comments on clarifying this would be appreciated.

<span style="text-decoration: underline;">Update - March 21, 2012</span>
One thing I have figured out is that a parameter defined inside a function can be called from within the script (just not outside the script like in the first example).

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps-function3.png"><img class="alignnone size-full wp-image-3612" title="ps-function3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps-function3.png" width="513" height="228" /></a>

<span style="text-decoration: underline;">Update #2 - March 21, 2012</span>
I think I finally understand what's going on after discussing with a developer friend of mine and running the function as shown below.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps-function4.png"><img class="alignnone size-full wp-image-3615" title="ps-function4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps-function4.png" width="640" height="98" /></a>

µ